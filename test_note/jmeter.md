## 一、安装

1.访问[JMeter官网](https://jmeter.apache.org/)（需 Java 8+ 环境）

2.JMeter环境变量配置

* 变量名：JMETER_HOME

​	变量值：E:\apache-jmeter-5.6.3

* Path：%JMETER_HOME%\bin

3.查看是否配置成功

* `jmeter -v`

4.启动

* `jmeter`

网站： **[JSONPlaceholder](https://jsonplaceholder.typicode.com/)**

运行命令

`jmeter -n -t "jmx/REST_API_Performance_Testing.jmx" -l "results/result.jtl" -e -o reports/`

- `-t "jmx/REST_API_Performance_Testing.jmx"`：从 `jmx/` 子目录加载测试计划。
- `-l results/result.jtl`：将结果文件保存到 `results/` 目录。
- `-o reports/`：将 HTML 报告生成到 `reports/` 目录。

**注：**报告目录必须为空**：
`-o reports/` 中的 `reports/` 目录需要是 **空目录或 **不存在**（JMeter 会自动创建）

`.jtl` 文件是原始数据，可重新生成报告：

`jmeter -g results/result.jtl -o reports`

给报告生成时间戳

```cmd
@echo off
set "date_part=%date:~0,4%%date:~5,2%%date:~8,2%"
set "time_part=%time:~0,2%%time:~3,2%"
set "time_part=%time_part: =0%"
set "datetime=%date_part%_%time_part%"
jmeter -g results/result.jtl -o "reports/report_%datetime%/
```

或者直接

```cmd
jmeter -g results/result.jtl -o "reports/report_%date:~0,4%%date:~5,2%%date:~8,2%%time:~0,2%%time:~3,2%/
```



>1. **日期部分（`date_part`）**：
>   - `%date:~0,4%` → 年（如 `2023`）
>   - `%date:~5,2%` → 月（如 `10`）
>   - `%date:~8,2%` → 日（如 `01`）
>   - 最终格式：`20231001`
>2. **时间部分（`time_part`）**：
>   - `%time:~0,2%` → 小时（如 `15`）
>   - `%time:~3,2%` → 分钟（如 `30`）
>   - `%time_part: =0%` → 将前导空格替换为 `0`（如 `9:30` → `09:30`）
>   - 最终格式：`1530`

## 二、练习网站

* [**httpbin.org**](https://httpbin.org/#/) 
* [**dummy.restapiexample.com**](https://dummy.restapiexample.com/)
* [**jsonplaceholder.typicode.com**](https://jsonplaceholder.typicode.com/)
* 



## 三、指标

### 1.聚合报告 Aggregate Report

| 字段名          | 描述                                               | **健康范围参考**                                             |
| --------------- | -------------------------------------------------- | ------------------------------------------------------------ |
| Label           | 标识测试的接口或场景                               |                                                              |
| #Samples        | 某个请求的总执行次数                               |                                                              |
| Average         | 所有请求的平均响应时间                             | 根据业务需求设定（如 ≤2000ms）                               |
| Median          | 50% 的请求响应时间低于此值                         | 与 Average 接近为佳                                          |
| 90% Line        | 90% 的请求响应时间低于此值                         | 建议 ≤ Average × 2                                           |
| 95% Line        | 95%的请求的响应时间                                |                                                              |
| 99% Line        | 99% 的请求响应时间低于此值（长尾效应指标）         | 若过高，需排查偶发慢请求                                     |
| Min/Maximum     | 最快和最慢响应时间                                 | Max 不应超过预期阈值的 3 倍                                  |
| Error %         | 请求失败的比例                                     | **严格场景**：≤ 0.1%（如支付、核心接口）; **一般场景**：≤ 1% |
| Throughput      | 每秒完成的请求数（Requests/sec），衡量系统处理能力 |                                                              |
| Received KB/sec | 每秒接收的数据量（服务器 → 客户端）                | 检查是否接近带宽上限                                         |
| Sent KB/sec     | 每秒发送的数据量（客户端 → 服务器）                | 大文件上传场景需特别关注                                     |

Error %  **常见原因**：

- 服务端资源不足（CPU、内存、连接池耗尽）。
- 代码逻辑缺陷（空指针、超时未处理）。
- 网络问题（DNS 解析失败、连接超时）

### **分析步骤与实战案例**

#### **步骤 1：优先级排序**

1. **先看 Error %**：若错误率 >1%，优先解决稳定性问题。
2. **再查响应时间**：确认是否满足 SLA（如 95% Line < 2s）。
3. **最后看吞吐量**：评估是否达到业务需求。

------

#### **步骤 2：对比分析**

- **横向对比**：不同接口的响应时间和错误率（如 `/post` 比 `/get` 慢 3 倍）。
- **纵向对比**：与历史测试结果对比（如版本升级后吞吐量下降 30%



## 四、知识点

### （一）认证方式

常见的认证方式有四种，分别是 **Basic**、**Digest**、**OAuth** 和 **Bearer**

#### 4.1.‌ **Basic Auth （HTTP Basic Authentication）**  

**语法：**`Authorization:base64(username:password)`

Basic Auth‌也称为 **HTTP 基本认证**，是**最简单的认证方式**，主要用于**HTTP**协议中，通过**用户名**和**密码**进行身份验证。其工作原理如下：

1. **认证过程**‌：当客户端访问一个受保护的资源时，如果没有提供认证信息，服务器会返回一个401 Unauthorized状态和一个WWW-Authenticate响应头，指示客户端需要通过何种认证机制进行认证。客户端收到401响应后，会提示用户输入用户名和密码，然后将用户名和密码以`username:password`的形式拼接成一个字符串，并通过Base64编码后放在HTTP的Authorization请求头中重新发送请求‌12。
2. **安全性问题**‌：Basic Auth的主要缺点是安全性较低。每次请求都需要传递用户名和密码，这些信息容易被监听和盗取。此外，应用本地还需要保存用户名和密码，存在安全风险。因此，许多服务提供商出于安全考虑，可能会限制或停止使用这种认证方式‌3。
3. ‌**应用场景**‌：Basic Auth适用于一些简单的、不需要高度安全性的应用场景。例如，一些小型网站或内部系统可能会使用这种认证方式来简化用户登录过程‌13。
4. ‌**替代方案**‌：为了提升安全性，可以使用‌ **OAuth** ‌等更安全的认证方式。OAuth通过授权码流程，允许应用在用户授权后访问资源，而不需要直接存储用户的用户名和密码，从而提高了安全

#### 4.2.Digest Auth  **摘要认证**

1. **qop**：`Quality of Protection`

**作用：**决定了客户端与服务器之间如何通过摘要认证交换数据，并指定了保护级别

- **防止重放攻击**：通过引入 `nonce`（一次性随机数）和计数器（`nc`）机制。
- **完整性保护**：可选对请求内容（如请求体）进行哈希计算，防止数据篡改

2. qop的取值

|    **取值**    |                           **含义**                           |
| :------------: | :----------------------------------------------------------: |
|   **`auth`**   |     仅验证客户端身份（默认值），不保护请求内容的完整性。     |
| **`auth-int`** | 验证身份的同时，对请求内容（如请求体）进行哈希计算，保护数据完整性。 |
|    **空值**    |         不使用 `qop`（兼容旧版本协议，安全性较低）。         |

3. nonce="MOSQZ0itBAA=44abb6784cc9cbfc605a5b0893d36f23de95fcff"，

   algorithm=MD5,

   qop="auth",

   nc=00000001,

   cnonce="082c875dcb2ca740"

   username = guest

   password = guest

   realm = DIGEST

   HA1 = MD5(username:realm:pasword) = (guest:DIGEST:guest) = D00452ECC5A7576AB95C0CE19F897F82

   HA2 = MD5(method:uri) = (get:/digest/) = 56F5C7BF8CE8C69E9493584E41989888

   response = MD5(HA1:nonce:nc:cnonce:qop:HA2) = (D00452ECC5A7576AB95C0CE19F897F82:MOSQZ0itBAA=44abb6784cc9cbfc605a5b0893d36f23de95fcff:00000001:082c875dcb2ca740:auth:56F5C7BF8CE8C69E9493584E41989888)



HA1 = MD5(username:realm:pasword) = ("guest":"DIGEST":"guest") = "854791F43D771243D854BD2FDCD0E051"

HA2 = MD5(method:uri) = ("GET":"/digest/") = 6BB934FACE424000972412D972B03274

response = MD5("HA1":"nonce":"nc":"cnonce":"qop":"HA2") "854791F43D771243D854BD2FDCD0E051":"MOSQZ0itBAA=44abb6784cc9cbfc605a5b0893d36f23de95fcff":00000001:"082c875dcb2ca740":auth:"6BB934FACE424000972412D972B03274") = D2712E5CA29C111838BF700D1CB50559



作用

#### 4.3.bearer

Bearer 认证，也称为**令牌认证**，是一种 HTTP 身份验证方法

格式`Authorization: Bearer <token> `

**概述** 

Bearer Token 是一种用于身份验证的访问令牌，它授权持有者（Bearer）访问资源的权限。当你向服务器发送请求时，你可以在请求头中携带 Bearer Token ，服务器会根据这个 Token 来验证你的身份并授权你所请求的操作。 它通常用于 **OAuth 2.0** 认证框架中，用来验证访问者的身份并授予其相应的权限。Bearer Token 是一种加密的字符串，客户端在每次请求时将其附加到 HTTP 请求头中，从而使服务器能够识别并授权该请求。 

**基本概念** 

Bearer Token 是一种无状态的、短期的、可撤销的凭证，它被设计用来在客户端与服务器之间传递身份验证信息。所谓无状态，意味着服务器不需要保存任何关于 Bearer Token 的会话信息，只需要在收到请求时验证该 Token 的有效性。这样做的好处是减少了服务器的负担，提升了系统的扩展性。 Bearer Token 通常是由身份提供者（Identity Provider, IdP）生成的，这些身份提供者可以是 OAuth 2.0 的授权服务器。客户端通过特定的授权流程（如授权码流程、密码凭证流程等）获取 Bearer Token，之后便可在调用受保护资源时使用该 Token。 

**工作原理** 

当用户成功登录后，服务器会生成一个 Bearer Token 并返回给客户端，客户端随后在发起请求时，会在 HTTP 头部包含这个 Token。 Bearer Token 在请求头中以 Bearer 关键字加上令牌本身的形式发送，。服务器接收到请求后，会检查请求头中的 Authorization 字段，如果它以 Bearer 关键字开头，服务器就会提取出后面的令牌，并使用令牌来验证请求的合法性和授权级别，确认无误后提供请求的资源。

### （二）状态码

#### 1.304

在HTTP协议中，304状态码（Not Modified）是一个非常常见的响应状态，它用来告诉客户端某个资源自上次请求后没有被修改过，因此可以使用本地缓存的版本。这通常发生在使用条件请求（Conditional Requests）的情况下，比如通过`If-Modified-Since`或`If-None-Match`头部。

##### 解释

1. **If-Modified-Since**: 客户端在请求头中包含`If-Modified-Since`字段，其值通常是一个时间戳，表示客户端缓存的资源的最后修改时间。服务器收到这个请求后，会检查资源的最后修改时间是否在这个时间戳之后。如果没有修改（即资源仍然是“未修改”的），服务器将返回304状态码，而不是重新发送整个资源。
2. **If-None-Match**: 这个头部通常用于处理资源的版本控制。客户端提供一个或多个实体标签（Entity Tags），通常是资源的ETag值。服务器会检查这些标签与当前资源的ETag是否匹配。如果ETag匹配（即资源未更改），服务器将返回304状态码。

## 五、实操

### 1.Auth

#### 1.1Basic Auth

**问题：**通过`${__base64encode(${user}:${passwd})}`不能进行`base64`编码

**解决1：**添加 `JSR223 PreProcessor`

```groovy
import java.util.Base64

def user = vars.get("user")
def passwd = vars.get("passwd")
def authString = "${user}:${passwd}"
def encodedAuth = Base64.getEncoder().encodeToString(authString.getBytes("UTF-8"))
vars.put("encodedAuth", encodedAuth)
```

**解决2：**通过`HTTP Authorization Manager`

| Base URL              | Username  | Password      | Mechanism |
| --------------------- | --------- | ------------- | --------- |
| `https://httpbin.org` | `${user}` | `${password}` | `BASIC`   |

**注：**`Base URL`不能写`httpbin.org`
