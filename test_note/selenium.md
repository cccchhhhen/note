## 一、安装

`pip install selenium`

`pip show selenium`

测试网站

* `https://www.saucedemo.com/` 商城（登录、购物）有时候打开很慢
* `https://parabank.parasoft.com/`  银行系统模拟（登录、转账等）
* `https://automationexercise.com/`  商城（购物）

## 二、例子

1.页面加载时间

```python
# 方法一：基于时间戳差值
    start_time = time.time()
    driver.get("https://www.saucedemo.com/")
    end_time = time.time()
    load_time = end_time - start_time
    print(f"home_page_load_time:{load_time:.2f}s")
```

>import time
>
>**格式化输出:**
>`:.2f` 将结果保留两位小数


```py
# 方法二：使用 Navigation Timing API（精确到各阶段）
driver.get("https://www.saucedemo.com/")
navigation_timing = driver.execute_script(
    "return window.performance.timing.toJSON();"
)
# 计算关键阶段时间
dom_content_loaded = (navigation_timing["domContentLoadedEventEnd"] - navigation_timing["navigationStart"]) / 1000
load_event_end = (navigation_timing["loadEventEnd"] - navigation_timing["navigationStart"]) / 1000

print(f"DOM 加载完成时间：{dom_content_loaded:.2f} 秒")
print(f"页面完全加载时间：{load_event_end:.2f} 秒")
```

>**关键对象**：
>- `window.performance.timing` 是浏览器提供的 `Navigation Timing API`（**旧版API**），记录了页面加载各阶段的时间戳（单位：毫秒）。**现代浏览器**推荐使用`PerformanceNavigationTiming`
>- `.toJSON()` 将其转换为 `JSON` 格式，便于 `Python` 处理
>
>**指标含义**：
>
>* `navigationStart` 开始导航时间（用户点击输入`url`或点击连接）
>* `DOMContentLoaded` 事件表示 **HTML 文档和 DOM 树已完全加载并解析完成**（无需等待图片、样式表等资源）
>* `load` 事件表示 **页面所有资源（图片、样式表、脚本等）已完全加载**
>
>

2.封装公共等待方法

```py
# 方法一：快速优化
# 封装为函数，减少重复使用
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC

def test_sauce_demo_login(driver):
    wait_and_send_keys(driver, (By.ID, 'user-name'), 'standard_user')

def wait_and_send_keys(driver, locator, text):
    WebDriverWait(driver, 10).until(
        EC.presence_of_element_located(locator)
    ).send_keys(text)
```



```py
# 方法二：使用Page Object模式
def test_sauce_demo_login(driver):
    login_page = LoginPage(driver)
    login_page.login('standard_user', 'secret_sauce')
    # 验证是否登录成功
    assert driver.current_url == "https://www.saucedemo.com/inventory.html"
    assert "Swag Labs" in driver.title

class LoginPage:
    USERNAME = (By.ID, 'user-name')
    PASSWORD = (By.ID, 'password')
    LOGIN_BUTTON = (By.ID, 'login-button')

    def __init__(self, driver):
        self.driver = driver

    def login(self, username, password):
        self._enter_username_(username)
        self._enter_password_(password)
        self._click_login_button()

    def _enter_username_(self, username):
        self.wait_and_send_keys(self.USERNAME, username)

    def _enter_password_(self, password):
        self.wait_and_send_keys(self.PASSWORD, password)

    def _click_login_button(self):
        WebDriverWait(self.driver, 10).until(
            EC.presence_of_element_located(self.LOGIN_BUTTON)
        ).click()

    def wait_and_send_keys(self, locator, text):
        WebDriverWait(self.driver, 10).until(
            EC.presence_of_element_located(locator)
        ).send_keys(text)
```



## 三、知识点

#### 3.1 元素定位

```py
from selenium.webdriver.common.by import By
```

**八大元素定位：**

`find_element(By.<策略>, "值")`

| 定位方式            | 语法                                                      | 说明                                      |
| ------------------- | --------------------------------------------------------- | ----------------------------------------- |
| `ID`                | `find_element(By.ID, "id_value")`                         |                                           |
| `name`              | `find_element(By.NAME, "name_value")`                     |                                           |
| `Class Name`        | `find_element(By.CLASS_NAME, "class_name")`               |                                           |
| `Tag Name`          | `find_element(By.TAG_NAME, "tag_name")`                   |                                           |
| `CSS`选择器         | `find_element(By.CSS_SELECTOR, "css_selector")`           |                                           |
| `XPath`             | `find_element(By.XPATH, "xpath_expression")`              |                                           |
| ` Link Text`        | `find_element(By.LINK_TEXT, "link_text")`                 | 通过链接的文本内容定位（适用于 <a> 标签） |
| `Partial Link Text` | `find_element(By.PARTIAL_LINK_TEXT, "partial_link_text")` | 通过链接的部分文本内容定位                |

注：`find_element_by_id`是旧方法

| `find_element_by_id` 适合 | `find_element(By.ID, ...)` 适合 |
| ------------------------- | ------------------------------- |
| 旧项目维护                | 新项目开发                      |
| 简单脚本开发              | Page Object 模式                |
| 教学示例 (展现直观性)     | 参数化定位器                    |
|                           | 自定义查找器扩展                |

##### 3.1.1 `XPath`

**注意：**`xpath`的定位 同一级别的多个标签索引从1开始

###### 1.标签属性定位

①**绝对路径：**用一个斜杠'/'

**last()**方法：当标签存在多个相同的时候，定位到最后一个；last() - 1 定位倒数第二个

eg：

```js
html/body/div/div/div[2]/div/div/div/form/div[last()-1]
```



②**相对路径：**以 '//' 开头

* `//标签名[index]`：`//input[1]`，[1]可省略，为什么[2]、[3]不行？

* 标签属性定位：`//标签名[@属性="属性值"]`

  * `name, class, type, id`都行,

  * `//input[@type='submit']`

  * 属性组合

    * `//标签名[@属性1="属性值1" and @属性2="属性值2"]`
    * `//input[@class="input_error form_input error" and @type="password"]`
    * 或者 `//input[contains(@class, 'form_input') and @type='password']`

  * 当标签元素少，但标签中间有文本可以定位

    * ```html
      <h4>Accepted usernames are:</h4>
      //h4[contains(text(), "Accepted")]
      ```

    * 有多个类名

    * 属性值太长，可通过模糊定位

      * `//a[contains(@href, "fonts")]`

  * 动态属性定位

    * `contains()`
    * `srarts-with`
      * `//input[starts-with(@id, 'login')]`
    * `ends-with`
      * **注：**`ends-with`是`XPath`2.0的，大部分浏览器可能不支持



③`//` 和 `.//` 的区别

`//` 是指从全文上下文中搜索//后面的节点，而 `.//` 则是指从前面的节点的子节点中进行查找

④选取若干路径 `|`

作用：匹配多个不同条件的元素

`//input[@type='text'] | //input[@type='password']`

###### 2.节点文本定位

* `text()`：`//div[@id="s-hotsearch-wrapper"]/ul/li[3]/a/span[contains(string(), '机器人')]`
* `string()`：`//div[@id="s-hotsearch-wrapper"]/ul/li[3]/a[contains(string(), '机器人')]`
* 区别：text() : 当前元素下的文本；string()：当前元素所有文本和子元素文本（字符串列表）

## 四、网站

[demoqa](https://demoqa.com/text-box)

### 1.radio button

编写测试用例：
测试用例1：验证初始状态

- 所有单选按钮都未选中（除了默认可能没有选中的，这里默认都没有选中）

- No按钮是禁用的

- ```py
  button.is_selected()  # 检验按钮是否被选中
  result_message.is_displayed()  # 检验消息是否有提示
  ```

测试用例2：选择“Yes”

- 点击“Yes”单选按钮

- 验证“Yes”被选中

- 验证其他按钮没有被选中

- 验证显示结果文本为“You have selected Yes”

- ```py
  # 切换回未选中状态(刷新)
  driver.refresh()
  ```

测试用例3：选择“Impressive”

- 点击“Impressive”
- 验证“Impressive”被选中
- 验证其他按钮（除No）没有被选中
- 验证结果文本变为“You have selected Impressive”

测试用例4：尝试选择“No”

- 验证“No”按钮是禁用的，无法点击。

- ```py
  assert no_radio.get_attribute("disabled") == "true", "No选项未被禁用"
  ```

测试用例5：切换选择（先Yes，再Impressive）

- 先选择Yes
- 再选择Impressive
- 验证Yes已取消，Impressive被选中

实现注意事项：

- 使用XPath或CSS选择器定位元素。
- 对于不可用的按钮，使用is_enabled()方法判断。
- 单选按钮的选中状态使用is_selected()方法。
- 结果文本显示在一个div（id='result'）中，需要等待其出现（如果动态显示）。
