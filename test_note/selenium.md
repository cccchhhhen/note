`pip install selenium`

`pip show selenium`

测试网站

* `https://www.saucedemo.com/` 商城（登录、购物）有时候打开很慢
* `https://parabank.parasoft.com/`  银行系统模拟（登录、转账等）
* `https://automationexercise.com/`  商城（购物）

一、例子

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



