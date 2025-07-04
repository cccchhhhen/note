### 功能

#### 1.限速

**A.设置：**

①右边菜单--> `FiddlerScript`

②`Rules --> Customize Rules`

```JS
if (m_SimulateModem) {
            // Delay sends by 300ms per KB uploaded.
            oSession["request-trickle-delay"] = "300"; 
            // Delay receives by 150ms per KB downloaded.
            oSession["response-trickle-delay"] = "150"; 
        }
```

> `oSession["request-trickle-delay"] = "300"`：默认发送数据，发送`1kb`数据需要的时间是`300ms`
>
> `oSession["response-trickle-delay"] = "150"`：默认接受数据，接受`1kb`数据延迟接受的时间是`150ms`

**B.启动：**

`Rules --> Performance --> Simulate Modem Speeds`

#### 2.篡改数据

篡改数据分两种：

**第一种：篡改请求的数据**，主要是检查这个接口处理情况，篡改请求的数据，适用于不方便直接去调接口，而且不关心页面显示，只看接口请求是否正常，也就是说看这个接口是否能正常处理我篡改后的数据，看它返回的结果是对还是不对的情况。

核心逻辑在于**绕过客户端限制，直接验证服务端（`API`/接口）自身的处理逻辑和健壮性**

>**【例子】**测试`www.ip138.com`的手机查询功能
>
>手机号码通常都是11位的，在网页上当输入的手机号码不够11位时，会提示：“不是完整的11位手机号或者正确的手机号前七位”的信息。手机号输入框在前端（网页或`App`）做了长度限制（必须11位），意味着通过页面我们是没法做对输入框做小于11位的测试，但我们通过接口就可以做到这种测试
>
>- **问题：** 后端接口是否也做了同样的校验？还是仅仅依赖前端校验？如果后端没有校验，攻击者可以直接构造非法请求发送给后端，可能导致系统错误、数据异常甚至安全漏洞
>- **Fiddler的作用：** 通过拦截并修改请求数据（比如把手机号改成10位），Fiddler允许测试人员**绕过前端限制**，直接发送一个“非法”请求到服务端
>
>**操作（绕过前端进行边界测试）：**
>
>1. 打开`Fiddler`和谷歌，在谷歌输入手机号码`15236542589`，不点击查询
>2. 在`Fiddler`中清空会话列表并且设置拦截（箭头向上）（点击下方空格）
>3. 谷歌中点击查询，在`Fiddler`中找到获取请求内容的HTTP，点击`Inspectors-->WebForms`，对请求的内容进行修改，将`mobile`改成`15236`
>4. 取消拦截（双击两次箭头），点击`Run to Completion`继续执行
>5. 网页相应结果为：验证手机号有误
>6. 正确响应说明接口没有问题，若是不能返回或者返回乱码，说明接口有问题

**第二种：篡改返回的数据**，返回的数据意味着我们关心页面上的显示（比如：页面上显示这个东西，返回的结果发生改变之后，页面是否正常显示，页面处理情况）

> **【例子】**当显示框里的字符有一百个多字符，如果溢出了会不会导致页面错乱
>
> **操作：**
>
> 1. 打开`Fiddler`和谷歌，在谷歌输入手机号码`15236542589`，不点击查询
> 2. 在`Fiddler`中清空会话列表并且设置拦截（箭头向上）（点击下方空格）
> 3. 谷歌中点击查询，在`Fiddler`中找到获取请求内容的HTTP，点击`Inspectors-->WebForms`，对请求的内容进行修改，将`mobile`改成`abcd`
> 4. 对响应结果拦截（点击一次箭头，箭头向下），点击`Run to Completion`继续执行
> 5. 修改返回前端的数据`SyntaxView`
> 6. 取消拦截（点击一次箭头），点击`Run to Completion`继续执行
> 7. 网页相应结果为：验证手机号有误，****（自定义内容）

#### 3.重定向

重定向是把一个资源定义成另一个资源

> **【例子】**将百度logo替换成其他图片
>
> **步骤：**
>
> 1. 打开Fiddler， 将会话列表清空； 打开百度首页，获取信息。
> 2. 找到百度图片资源（在`WebView`中看），可通过`F12`查看比较，选中请求
> 3. `Fiddler-->AutoResponder-->√Enable rules  √Accept all CONNECTs  √Unmatched requests passthrough` --> 替换图片 --> `save` --> 刷新百度页面
> 4. **注：**替换的是你本地上的数据，百度的logo实际上是没有换掉的，除了图片，也能重定向其他资源

- `Enable rules` 启用规则
- `Unmatched requests passthrough` 没有匹配到的请求予以通过（建议开启；没开启时，规则匹配外的请求将失败，报HTTP 404错误）
- `Enable Lagency` 延迟响应时间，单位 ms（毫秒)

#### 4.发送自定义请求

自定义请求是不通过浏览器，Fiddler直接发送请求到服务器

> **【例子】**`www.ip138.com`的手机查询功能
>
> **步骤：**
>
> 1. 在查询网站输入手机号`15632458761`并点击查询，在`Fiddler`中找到该请求
> 2. 点击左下角的`Capturing`为空，点击右边菜单中的`Composer`
> 3. 将请求拖到`Composer`中，拖过去的时候边框会变绿，会话框其他请求可删除
> 4. `Composer`中的内容可随意更改，如`mobile=15236`，点击右边的执行`Execute`
> 5. 在左边的会话列表中会出现服务器返回的结果，双击可查看结果