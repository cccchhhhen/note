### **一、需求分析**(Markdown )

#### **1. 测试目标**

- 验证网站核心功能（登录、商品浏览、购物车管理、结账流程）的正确性。
- 确保跨浏览器、跨设备的兼容性。
- 验证性能（响应时间、并发处理）和安全性（数据加密、输入验证）。
- 保证用户体验（界面一致性、错误提示友好性）。

#### **2. 功能模块**

- 用户登录
  - 有效/无效凭据验证。
  - 锁定账户处理。
- 商品浏览
  - 商品列表展示、排序（价格、名称）、过滤。
- 购物车管理
  - 添加/删除商品、数量修改。
- 结账流程
  - 表单填写验证、订单确认、库存更新。

#### **3. 非功能需求**

- **兼容性**：支持 Chrome、Firefox、Safari 及移动端响应式布局。
- **性能**：关键页面加载时间 ≤2秒，支持 50+ 并发用户。
- **安全性**：HTTPS 加密、敏感数据（如密码）加密存储。
- **用户体验**：导航直观、错误提示明确、界面适配不同屏幕。

### **二、测试用例设计(Excel)**

#### **(一)、用户身份模块 (20+ 用例)**

1. **登录功能**
   - 使用有效用户名/密码登录成功
   - 空用户名/空密码登录失败
   - 用户名正确但密码错误
   - 密码正确但用户名不存在
   - 锁定用户尝试登录（触发账户锁定机制）
   - 登录后检查 Session 有效期
   - 登录后页面跳转正确性
   - 浏览器 Cookie 存储安全验证
   - 登录表单的 XSS 注入防御测试
2. **注册功能**
   - 新用户注册成功（邮箱验证）
   - 注册时邮箱格式错误（如缺少 @ 符号）
   - 注册时密码强度不足（如仅数字）
   - 重复邮箱注册失败
   - 注册后自动登录功能验证
3. **用户权限**
   - 普通用户无法访问管理员后台
   - 管理员用户可管理商品和订单
   - 用户角色切换后的权限更新

------

#### **(二)、商品模块 (15+ 用例)**

1. **商品展示**
   - 商品列表分页功能（每页显示数量正确）
   - 商品排序（价格从低到高、销量降序）
   - 商品搜索（关键字匹配、模糊搜索）
   - 商品详情页信息完整性（价格、库存、描述）
   - 库存为0的商品显示“缺货”标识
2. **商品管理（管理员）**
   - 添加新商品（必填字段校验）
   - 编辑商品信息后保存成功
   - 删除商品后列表更新
   - 批量导入商品数据（CSV/Excel 文件）
3. **促销活动**
   - 限时折扣价格展示正确性
   - 满减活动自动触发（如满100减20）
   - 优惠券叠加使用规则验证

------

#### **(三)、购物车模块 (15+ 用例)**

1. **基础功能**
   - 添加单个商品到购物车
   - 批量添加多个商品到购物车
   - 删除购物车中的商品
   - 清空购物车功能
   - 修改商品数量（手动输入/增减按钮）
2. **数据同步**
   - 用户登录前后购物车数据同步
   - 多终端（浏览器/移动端）购物车同步
   - 库存变化后购物车中商品状态更新（如库存不足提示）
3. **异常场景**
   - 超时未操作后购物车数据保留
   - 购物车商品总价计算错误（如四舍五入问题）
   - 并发操作导致商品数量冲突

------

#### **(四)、订单模块 (20+ 用例)**

1. **下单流程**
   - 正常下单流程（选择地址、支付方式）
   - 下单时自动使用默认收货地址
   - 订单金额计算（商品总价 + 运费 - 优惠）
   - 下单后库存减少验证
2. **订单状态**
   - 订单状态流转（待支付 → 已支付 → 已发货 → 已完成）
   - 取消订单后库存回滚
   - 超时未支付订单自动取消
3. **支付集成**
   - 支付宝/微信支付成功回调验证
   - 支付失败后订单状态回滚
   - 支付金额篡改攻击防御测试
4. **发票与物流**
   - 电子发票信息自动生成
   - 物流单号录入后跟踪信息同步

------

#### **(五)、异常与边界测试 (10+ 用例)**

1. **边界值**
   - 商品数量输入最大值（如999件）
   - 价格字段输入负数或非数字字符
   - 超长文本输入（如地址栏输入1000字符）
2. **异常处理**
   - 服务端宕机时前端友好提示
   - 网络中断后订单提交重试机制
   - 数据库连接失败时的回滚机制
3. **性能与压力**
   - 高并发下单（如秒杀场景）
   - 购物车加载时间不超过2秒

------

#### **(六)、安全测试 (5+ 用例)**

1. SQL 注入攻击防御（如 `' OR 1=1 --`）
2. XSS 脚本攻击拦截（如 `<script>alert(1)</script>`）
3. CSRF Token 验证机制
4. 敏感信息加密传输（如密码、支付信息）
5. 越权访问测试（如直接访问其他用户订单详情页）

------

### **(七)、跨平台/浏览器测试**

1. **浏览器兼容性**
   - Chrome/Firefox/Edge/Safari 渲染一致性
   - 移动端浏览器页面适配
2. **响应式设计**
   - 不同屏幕尺寸（PC/平板/手机）布局适配
   - 触屏操作（滑动、缩放）兼容性

#### **1. 用户登录**（完成）

| **用例ID**  |   **场景**   |                           **步骤**                           |                         **预期结果**                         |
| :---------: | :----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| SD-LOGIN-01 | 有效用户登录 | 1. 输入有效用户名（standard_user）和密码（secret_sauce）。 2. 点击登录。 |                    成功跳转到商品列表页。                    |
| SD-LOGIN-02 |   无效密码   |         1. 输入有效用户名和错误密码。 2. 点击登录。          | 显示错误提示：“Epic sadface: Username and password do not match any user in this service”。 |
| SD-LOGIN-03 |    空登录    |                  1.用户密码为空。2.点击登录                  |      显示错误提示：“Epic sadface: Username is required”      |
| SD-LOGIN-04 | 锁定用户登录 |   1. 输入锁定用户（locked_out_user）和密码。 2. 点击登录。   | 显示错误提示：“Epic sadface: Sorry, this user has been locked out.” |



#### **2. 商品浏览**

##### 2.1. 商品列表展示

| **用例ID** |     **场景**     |             **测试步骤**              |                         **预期结果**                         | **优先级** |
| :--------: | :--------------: | :-----------------------------------: | :----------------------------------------------------------: | :--------: |
| SD-PROD-01 | 商品列表默认加载 | 1. 用户登录成功。 2. 进入商品列表页。 | 页面正确加载所有商品项，包含名称、价格、图片和“Add to Cart”按钮。 |     P0     |
| SD-PROD-02 | 商品图片显示验证 |  1. 检查每个商品的图片元素是否加载。  |            所有商品图片正常显示，无破损或占位符。            |     P1     |



##### 2.2. **商品排序功能**（完成）

| **用例ID** |    **场景**    |               **测试步骤**               |       **预期结果**       | **优先级** |
| :--------: | :------------: | :--------------------------------------: | :----------------------: | :--------: |
| SD-SORT-01 | 按价格升序排序 | 1. 选择排序方式为“Price (low to high)”。 | 商品按价格从小到大排列。 |     P0     |
| SD-SORT-02 | 按价格降序排序 | 1. 选择排序方式为“Price (high to low)”。 | 商品按价格从大到小排列。 |     P0     |
| SD-SORT-03 | 按名称A-Z排序  |    1. 选择排序方式为“Name (A to Z)”。    | 商品按名称字母顺序排列。 |     P0     |
| SD-SORT-04 | 按名称Z-A排序  |    1. 选择排序方式为“Name (Z to A)”。    | 商品按名称字母逆序排列。 |     P0     |



##### 2.3 **商品详情页功能**（完成）

|  **用例ID**  |   **场景**   |              **测试步骤**               |                        **预期结果**                        | **优先级** |
| :----------: | :----------: | :-------------------------------------: | :--------------------------------------------------------: | :--------: |
| SD-DETAIL-01 | 查看商品详情 |       1. 点击任意商品名称或图片。       | 跳转到详情页，显示完整描述、价格和“Back to products”链接。 |     P0     |
| SD-DETAIL-02 | 返回商品列表 | 1. 在详情页点击“Back to products”链接。 |       返回商品列表页，页面状态保留（如排序、过滤）。       |            |

#### **3. 购物车管理**

##### 3.1 商品添加功能（完成）

|   用例ID   |         场景         |                           测试步骤                           |                 预期结果                  | 优先级 |
| :--------: | :------------------: | :----------------------------------------------------------: | :---------------------------------------: | :----: |
| SD-CART-01 | 添加单个商品到购物车 | 1. 登录成功 2. 在商品列表页点击某商品的“Add to Cart”按钮 3. 进入购物车页面 |   购物车中显示该商品，数量为1，总价正确   |   P0   |
| SD-CART-02 |   添加多个不同商品   |           1. 连续添加3个不同商品 2. 进入购物车页面           | 购物车中显示3个商品，总价为各商品价格之和 |   P0   |

##### 3.2**商品移除功能**（完成）

|   用例ID   |     场景     |               测试步骤                |          预期结果          | 优先级 |
| :--------: | :----------: | :-----------------------------------: | :------------------------: | :----: |
| SD-CART-03 | 移除单个商品 | 1. 购物车中有商品 2. 点击“Remove”按钮 | 商品从购物车消失，总价更新 |   P0   |
| SD-CART-04 |  清空购物车  |            1. 移除所有商品            | 购物车显示“Cart is empty”  |   P1   |

##### 3.3**商品数量修改（不支持）**

|   用例ID   |     场景     |                 测试步骤                 |          预期结果           | 优先级 |
| :--------: | :----------: | :--------------------------------------: | :-------------------------: | :----: |
| SD-CART-05 | 增加商品数量 | 1. 修改某商品数量为3 2. 点击“Update”按钮 | 商品数量变为3，总价正确更新 |   P1   |
| SD-CART-06 | 减少商品数量 |    1. 修改数量为0 2. 点击“Update”按钮    |    商品被移除，总价更新     |   P1   |

##### 3.4**页面跳转功能**

|   用例ID   |            场景             |                测试步骤                 |                预期结果                | 优先级 |
| :--------: | :-------------------------: | :-------------------------------------: | :------------------------------------: | :----: |
| SD-CART-07 | 点击“Continue Shopping”按钮 |          1. 从购物车页点击按钮          | 返回商品列表页，保留之前状态（如排序） |   P1   |
| SD-CART-08 |     点击“Checkout”按钮      | 1. 购物车中有商品 2. 点击“Checkout”按钮 |          跳转到结账信息填写页          |        |

#### **4. 结账流程**

##### 4.1. **表单验证（Checkout Information）**

|     用例ID     |        场景        |                           测试步骤                           |                    预期结果                    | 优先级 |
| :------------: | :----------------: | :----------------------------------------------------------: | :--------------------------------------------: | :----: |
| SD-CHECKOUT-01 |  必填字段为空提交  |     1. 进入结账页 2. 不填写任何信息，点击“Continue”按钮      | 显示错误提示：“Error: First Name is required”  |   P0   |
| SD-CHECKOUT-02 | 仅填写部分必填字段 |        1. 填写 First Name，其他留空 2. 点击“Continue”        |  显示错误提示：“Error: Last Name is required”  |   P0   |
| SD-CHECKOUT-03 | 仅填写部分必填字段 | 1. 填写有效 First/Last Name，Zip Code 留空 2. 点击“Continue” | 显示错误提示：“Error: Postal Code is required” |   P1   |

##### **4.2. 订单信息确认（Checkout Overview）**

|     用例ID     |          场景          |                  测试步骤                   |               预期结果               | 优先级 |
| :------------: | :--------------------: | :-----------------------------------------: | :----------------------------------: | :----: |
| SD-CHECKOUT-04 |    成功提交有效信息    |  1. 正确填写所有必填字段 2. 点击“Continue”  | 跳转到订单概览页，显示商品、价格总计 |   P0   |
| SD-CHECKOUT-05 |    验证商品总价计算    | 1. 结账前购物车有多个商品 2. 进入订单概览页 |      总价 = 商品价格总和 + 税费      |   P0   |
| SD-CHECKOUT-06 | 取消订单返回商品列表页 |       1. 在订单概览页点击“Cancel”按钮       |            返回商品列表页            |   P1   |

##### **4.3. 订单完成（Checkout Complete）**

|     用例ID     |     场景     |              测试步骤              |                预期结果                 | 优先级 |
| :------------: | :----------: | :--------------------------------: | :-------------------------------------: | :----: |
| TC-CHECKOUT-07 | 完成订单流程 |  1. 在订单概览页点击“Finish”按钮   | 显示“Thank you for your order!”确认信息 |   P0   |
| TC-CHECKOUT-08 |   返回首页   | 1. 在订单完成页点击“Back Home”按钮 |             返回商品列表页              |        |



### 三、脚本

#### 1.登录页面

```py
# pages/login_page.py
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC

class LoginPage:

    # 元素定位器
    USERNAME = (By.ID, 'user-name')
    PASSWORD = (By.ID, 'password')
    LOGIN_BUTTON = (By.ID, 'login-button')
    ERROR_MESSAGE = (By.CSS_SELECTOR, "[data-test='error']")

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

    def get_error_message(self):
        return WebDriverWait(self.driver,10).until(EC.visibility_of_element_located(self.ERROR_MESSAGE)).text

    def wait_and_send_keys(self, locator, text):
        WebDriverWait(self.driver, 10).until(
            EC.presence_of_element_located(locator)
        ).send_keys(text)
```



```python
# conftest.py
from selenium import webdriver
import pytest
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.firefox.service import Service as FirefoxService
from selenium.webdriver.edge.service import Service as EdgeService
from webdriver_manager.chrome import ChromeDriverManager
from webdriver_manager.firefox import GeckoDriverManager
from webdriver_manager.microsoft import EdgeChromiumDriverManager

def pytest_addoption(parser):
    parser.addoption(
        "--browser",          # 命令行参数名称（以 `--` 开头）
        action="store",       # 存储参数值（默认动作）
        default="chrome",     # 未指定参数时的默认值
        help="chrome/firefox/edge"  # 在 `pytest --help` 中显示的帮助信息
    )

@pytest.fixture(scope="class")
def driver(request):
    browser = request.config.getoption("--browser").lower()

    if browser == "chrome":
        options = webdriver.ChromeOptions()
        options.add_argument("--headless")  # 无头模式可选
        chrome_service = ChromeService(ChromeDriverManager().install())
        driver = webdriver.Chrome(service=chrome_service, options=options)
    elif browser == "firefox":
        firefox_service = FirefoxService(GeckoDriverManager().install())
        driver = webdriver.Firefox(service=firefox_service)
    elif browser == "edge":
        edge_service = EdgeService(EdgeChromiumDriverManager().install())
        driver = webdriver.Edge(service=edge_service)
    else:
        raise ValueError(f"Unsupported browser: {browser}")
    driver.get("https://saucedemo.com/")
    yield driver

```





```py
# tests/test_login.py
import pytest
from pages.login_page import LoginPage

# 测试数据（用户名、密码、预期结果）
TEST_DATA = [
    ("standard_user", "secret_sauce", "success"),  # 有效登录
    ("standard_user", "wrong_password", "Epic sadface: Username and password do not match any user in this service"),  # 无效密码
    ("locked_out_user", "secret_sauce", "Epic sadface: Sorry, this user has been locked out."),  # 锁定用户
    ("","", "Epic sadface: Username is required") # 无效登录 空
]

@pytest.mark.parametrize("username, password, expected", TEST_DATA)
def test_sauce_demo_login(driver, username, password, expected):
    login_page = LoginPage(driver)
    login_page.login(username, password)
    if expected == "success":
        assert "inventory" in driver.current_url
    else:
        assert login_page.get_error_message() == expected

```



执行

**分别执行不同的浏览器**

`pytest tests/test_login.py --browser=chrome`
`pytest tests/test_login.py --browser=firefox`

**多浏览器并行测试**

```bash
# 安装插件
pip install pytest-xdist

# 并行执行 Chrome 和 Firefox
pytest tests/ --browser=chrome --numprocesses=2
pytest tests/ --browser=firefox --numprocesses=2
```



#### 2.产品页面

##### 2.1.pages

```py
# pages/product_page.py
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.select import Select


class ProductPage:
    def __init__(self, driver):
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    # 元素定位器
    PRODUCT_ITEMS = (By.CLASS_NAME, "inventory_item")
    PRODUCT_NAMES = (By.CLASS_NAME, "inventory_item_name")
    PRODUCT_PRICES = (By.CLASS_NAME, "inventory_item_price")
    ADDED_PRODUCE_PRICES = (By.XPATH, "//button[text()='Remove']/preceding-sibling::div[@class='inventory_item_price']")
    SORT_DROPDOWN = (By.CLASS_NAME, "product_sort_container")
    FILTER_INPUT = (By.CSS_SELECTOR, ".product_filter_container input")
    # SEARCH_BAR = (By.ID, "search-box")
    SEARCH_RESULT_MSG = (By.CLASS_NAME, "search_results_message")
    # PAGINATION = (By.CLASS_NAME, "pagination")
    DETAIL_BACK_BUTTON = (By.ID, "back-to-products")

    # ADD_TO_CART_BUTTONS = (By.CLASS_NAME, "btn_inventory")
    ADD_TO_CART_BUTTONS = (By.XPATH, "//button[text()='Add to cart']")
    CART_ICON = (By.CLASS_NAME, "shopping_cart_link")
    CART_ICON_COUNT = (By.CLASS_NAME, "shopping_cart_badge")

    def get_product_names(self):
        """获取所有商品名称"""
        return [item.text for item in self.driver.find_elements(*self.PRODUCT_NAMES)]

    def get_product_prices(self):
        """获取所有商品价格"""
        prices = self.driver.find_elements(*self.PRODUCT_PRICES)
        return [float(price.text.replace("$", "")) for price in prices]

    def get_added_product_price(self):
        """获取添加到购物车的商品的价格"""
        # prices = self.driver.find_elements(*self.ADDED_PRODUCE_PRICES)
        try:
            # 等待元素加载
            WebDriverWait(self.driver, 10).until(
                EC.presence_of_element_located(self.ADDED_PRODUCE_PRICES)
            )
            prices = self.driver.find_elements(*self.ADDED_PRODUCE_PRICES)
            print(f"prices:{prices}")
            return [float(price.text.replace("$", "")) for price in prices]
        except Exception as e:
            print(f"出错啦：{e}")

    def sort_products(self, sort_type):
        """选择排序方式"""
        # 1. 定位下拉框元素
        dropdown = self.driver.find_element(*self.SORT_DROPDOWN)
        # 2. 创建Select对象并选择指定选项
        Select(dropdown).select_by_visible_text(sort_type)

    def open_product_detail(self, product_name):
        """打开指定商品详情页"""
        for item in self.driver.find_elements(*self.PRODUCT_NAMES):
            if item.text == product_name:
                item.click()
                break
        else:
            raise ValueError(f"Product {product_name} not found")

    def get_search_result_message(self):
        """获取搜索结果提示信息"""
        return self.driver.find_element(*self.SEARCH_RESULT_MSG).text

    def add_product_to_cart(self, index=0):
        """添加指定索引的商品到购物车"""
        # print("self.ADD_TO_CART_BUTTONS",self.driver.find_elements(*self.ADD_TO_CART_BUTTONS)[index])
        # buttons = self.driver.find_elements(*self.ADD_TO_CART_BUTTONS)
        # print("找到的按钮数量:", len(buttons))
        # WebDriverWait(self.driver, 10).until(
        #     EC.presence_of_element_located()
        # )
        self.driver.find_elements(*self.ADD_TO_CART_BUTTONS)[index].click()
        buttons = self.driver.find_elements(*self.ADD_TO_CART_BUTTONS)
        # print("剩余按钮的数量:", len(buttons))

    def get_cart_item_count(self):
        """获取购物车图标上的商品数量"""
        try:
            return int(self.driver.find_element(*self.CART_ICON_COUNT).text)
        except:
            return 0  # 购物车为空时返回0

    def open_cart(self):
        """打开购物车页面"""
        self.driver.find_element(*self.CART_ICON).click()
```

###### 2.1.1.知识点

> Web 页面上操作下拉选择框（`<select>` 元素），通过 **Selenium 的 `Select` 类** 实现按可见文本选择排序方式。
>
>### **1. `dropdown = self.driver.find_element(\*self.SORT_DROPDOWN)`**
>
>- **作用**: 使用 Selenium 查找页面上的下拉框元素。
>- **`*` 操作符**: 解包元组，将 元组转换为两个独立参数传递给 `find_element` 方法。
>- **`find_element` 方法**: 返回匹配的第一个 `WebElement`（下拉框元素）。
>
>------
>
>### **2. `Select(dropdown).select_by_visible_text(sort_type)`**
>
>- **作用**: 创建一个 `Select` 对象，并基于选项的 **可见文本** 选择对应条目。
>
> - **`Select` 类**: Selenium 提供的工具类，专门用于操作传统的 HTML `<select>` 下拉框。
>
>  - `select_by_visible_text("文本")`
>
>  - 其他常用方法
>
>    - `select_by_value("value属性值")`: 按选项的 `value` 属性选择。
>- `select_by_index(索引号)`: 按选项的索引位置（从0开始）选择。



>**元素定位 `XPath`**
>
>1. `ADD_TO_CART_BUTTONS = (By.XPATH, "//button[text()='Add to Cart']")`
>
>**解析**：
>
>- `//button`：选择页面中所有 `<button>` 元素。
>- `[text()='Add to Cart']`：过滤出按钮的 **文本内容完全等于** "Add to Cart" 的元素
>
>2. `ADDED_PRODUCE_PRICES = (By.XPATH, "//button[text()='Remove']/preceding-sibling::div[@class='inventory_item_price']")`
>
>   * **定位兄弟节点**：
>     - `following-sibling::`：选择当前节点**之后**的所有同级兄弟节点。
>     - `preceding-sibling::`：选择当前节点**之前**的所有同级兄弟节点
>     - eg:`//button[text()='Remove']/following-sibling::*`  找到后面所有兄弟节点
>
>3. #### **浏览器生成的 `XPath` 特点**
>
>  通过 `Chrome DevTools` 复制的 `XPath` 通常是 **绝对路径**，且包含索引和复杂层级
>
>4. ### **特殊情况处理**
>
>  #### **4.1 文本包含空格或换行**
>
>  如果按钮文本包含不可见字符，使用 `contains()` 函数：
>
>  ```py
>  ADD_TO_CART_BUTTONS = (By.XPATH, "//button[contains(text(), 'Add to Cart')]")
>  ```
>
>  #### **4.2多语言支持**
>
>  如果页面支持多语言，应避免直接使用文本定位，改用 `data-testid` 等专用属性：
>
>  ```html
>  <button data-testid="add-to-cart-button">Add to Cart</button>
>  ```
>
>  ```py
>  ADD_TO_CART_BUTTONS = (By.XPATH, "//button[@data-testid='add-to-cart-button']")
>  ```
>
>5.**验证方法**：
>在浏览器开发者工具中执行
>
>`$x("//button[text()='Add to cart']")`

##### 2.2.tests

```py
# tests/test_products.py
import pytest
import allure
from pages.login_page import LoginPage
from pages.product_page import ProductPage


@allure.feature("商品浏览模块")
@pytest.mark.usefixtures("driver")
class TestProductBrowsing:
    @pytest.fixture(autouse=True,scope="function")
    def setup(self, driver):
        """登录并进入商品列表页"""
        self.driver = driver
        self.login_page = LoginPage(driver)
        self.login_page.login("standard_user", "secret_sauce")
        self.product_page = ProductPage(driver)
        # 每次测试前确保在商品列表页
        # self.driver.get("https://www.saucedemo.com/inventory.html")  # 新增

    @allure.story("验证商品排序功能")
    @pytest.mark.parametrize("sort_type, expected_order", [
        ("Price (low to high)", "asc"),
        ("Price (high to low)", "desc"),
        ("Name (A to Z)", "asc"),
        ("Name (Z to A)", "desc")
    ])
    def test_product_sorting(self, sort_type, expected_order):
        try:
            with allure.step(f"选择排序方式: {sort_type}"):
                self.product_page.sort_products(sort_type)

            if "Price" in sort_type:
                prices = self.product_page.get_product_prices()
                sorted_prices = sorted(prices, reverse=(expected_order == "desc"))
                assert prices == sorted_prices, "商品价格排序错误"
            else:
                names = self.product_page.get_product_names()
                sorted_names = sorted(names, reverse=(expected_order == "desc"))
                assert names == sorted_names, "商品名称排序错误"
        except Exception as e:
            self.driver.save_screenshot(f"error_{sort_type}.png")
            print("当前URL:", self.driver.current_url)
            print("页面标题:", self.driver.title)
            print("商品数量:", len(self.product_page.get_product_names()))
            raise e

    @allure.story("验证商品详情页跳转")
    def test_product_detail_navigation(self):
        product_name = "Sauce Labs Bolt T-Shirt"
        self.product_page.open_product_detail(product_name)
        assert "inventory-item" in self.product_page.driver.current_url, "未跳转到详情页"
        self.product_page.driver.find_element(*self.product_page.DETAIL_BACK_BUTTON).click()
        assert "inventory.html" in self.product_page.driver.current_url, "返回商品列表失败"
```

###### 2.2.1知识点

>**Allure步骤报告**
>
>- 在 **Allure 测试报告框架** 中，`@allure.feature` 和 `@allure.story` 是两个用于 **组织测试用例层级** 的装饰器，目的是让测试报告更清晰地展示功能模块和用户场景的归属关系
>
>  - ### **`@allure.feature`：定义功能模块**
>
>    - **作用**：标记测试用例所属的 **核心功能模块**（例如“商品浏览模块”）。
>    - **场景**：用于归类同一业务功能的所有测试用例
>
>  - ### **`@allure.story`：定义用户场景**
>
>    - **作用**：标记测试用例对应的 **具体用户场景或子功能**（例如“验证商品排序功能”）。
>    - **场景**：细化功能模块下的具体测试场景，体现用户需求
>
>  - ### **层级关系**
>
>    - `Feature > Story > Test Case`
>    - **`Feature`**：大的功能模块（例如“商品浏览”、“购物车”）。
>    - **`Story`**：功能模块下的具体场景（例如“排序”、“搜索”）。
>    - **`Test Case`**：具体的测试用例（例如“按价格升序排序”）
>
>- 使用`with allure.step()`包裹关键操作，在测试报告中生成可视化步骤节点
>
>**核心排序逻辑**
>
>- **价格排序分支** 
> - 使用Python内置`sorted()`生成理论排序结果
> - `sorted(list, reverse=True/False)`智能控制排序方向
>
>**亮点**
>
>* **参数化驱动 **   `@pytest.mark.parametrize`

#### 3.购物车页面

```python
# pages/cart_page.py
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC


class CartPage:
    def __init__(self, driver):
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    # 元素定位器
    CART_ITEMS = (By.CLASS_NAME, "cart_item")
    ITEM_NAMES = (By.CLASS_NAME, "inventory_item_name")
    ITEM_PRICES = (By.CLASS_NAME, "inventory_item_price")
    REMOVE_BUTTONS = (By.XPATH, "//button[text()='Remove']")
    CHECKOUT_BUTTON = (By.ID, "checkout")
    CONTINUE_SHOPPING_BTN = (By.ID, "continue-shopping")
    CART_QUANTITY = (By.CLASS_NAME, "cart_quantity")
    SINGLE_PRICE = (By.CLASS_NAME, "inventory_item_price")
    TOTAL_PRICE = (By.CLASS_NAME, "summary_total_label")
    # 购物车中商品价格的定位器

    def get_cart_items(self):
        """获取购物车中所有商品名称"""
        return [item.text for item in self.driver.find_elements(*self.ITEM_NAMES)]

    def get_cart_prices(self):
        """获取购物车中所有商品价格"""
        return [float(price.text.replace("$", ""))
                for price in self.driver.find_elements(*self.ITEM_PRICES)]

    def remove_item(self, index=0):
        """移除指定索引的商品"""
        buttons = self.driver.find_elements(*self.REMOVE_BUTTONS)
        print("找到的删除按钮数量:", len(buttons))
        self.driver.find_elements(*self.REMOVE_BUTTONS)[index].click()

    def proceed_to_checkout(self):
        """点击结账按钮"""
        self.driver.find_element(*self.CHECKOUT_BUTTON).click()

    def continue_shopping(self):
        """点击继续购物按钮"""
        self.driver.find_element(*self.CONTINUE_SHOPPING_BTN).click()

    def cal_total_price(self):
        """计算购物车总价"""
        try:
            # 等待元素加载
            WebDriverWait(self.driver, 10).until(
                EC.presence_of_element_located(self.SINGLE_PRICE)
            )
            prices = self.driver.find_elements(*self.SINGLE_PRICE)
            print(f"prices:{prices}")
            return [float(price.text.replace("$", "")) for price in prices]
        except Exception as e:
            print(f"出错啦：{e}")
    def get_total_price(self):
        """获取购物车总价"""

        return float(self.driver.find_element(*self.TOTAL_PRICE).text.replace("Total: $", ""))
```

```py
# tests/test_cart.py

import allure
import pytest
from pages.login_page import LoginPage
from pages.product_page import ProductPage
from pages.cart_page import CartPage
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC


@allure.feature("购物车管理")
@pytest.mark.usefixtures("driver")
class TestCart:
    @pytest.fixture(autouse=True, scope="function")
    def setup(self, driver):
        """初始化并登录"""
        self.driver = driver
        self.login_page = LoginPage(driver)
        self.product_page = ProductPage(driver)
        self.cart_page = CartPage(driver)
        # 登录并跳转到商品列表页
        self.login_page.login("standard_user", "secret_sauce")
        # self.driver.get("https://www.saucedemo.com/inventory.html")

    @allure.story("添加商品到购物车")
    def test_add_to_cart(self):
        for item_index in range(0,2):
            with allure.step(f"添加第{item_index + 1}个商品到购物车"):
                self.product_page.add_product_to_cart(item_index)
                # 验证购物车图标数量更新
                assert self.product_page.get_cart_item_count() == item_index + 1

            with allure.step("进入购物车验证商品"):
                self.product_page.open_cart()
                items = self.cart_page.get_cart_items()
                assert len(items) == item_index + 1, f"购物车应有{item_index + 1}个商品，实际为{len(items)}"
                self.cart_page.continue_shopping()


    @allure.story("从购物车移除商品")
    def test_remove_from_cart(self):
        # 添加两个商品
        items = 4
        for i in range(0,items):
            self.product_page.add_product_to_cart()

        # 移除商品
        try:
            # 进入购物车
            self.product_page.open_cart()

            for i in range(0,items):
                # time.sleep(2)
                with allure.step(f"移除第{i+1}个商品"):
                    self.cart_page.remove_item()
                    assert len(self.cart_page.get_cart_items()) == items-i-1
        except Exception as e:
            print(f"出错啦：{e}")


    @allure.story("验证总价计算")
    def test_total_price_calculation(self):
        # 添加三个商品
        items = 3
        for i in range(items):
            self.product_page.add_product_to_cart()

        prices = self.product_page.get_added_product_price()
        # print(f"添加商品后的价格为:{prices}")

        # 打开购物车
        self.product_page.open_cart()
        # 产品页面计算总价
        product_total_price = sum(prices)
        # 购物车页面计算总价
        actual_total = self.cart_page.cal_total_price()
        cart_total_price = sum(actual_total)
        assert product_total_price == cart_total_price, f"总价计算错误，预期: {product_total_price}, 实际: {cart_total_price}"

    @allure.story("继续购物功能")
    def test_continue_shopping(self):
        self.product_page.open_cart()
        self.cart_page.continue_shopping()
        assert "inventory.html" in self.driver.current_url
```

#### 4.结账页面

```py
# pages/checkout_page.py
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class CheckoutPage:
    def __init__(self, driver):
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    # 元素定位器
    FIRST_NAME = (By.ID, "first-name")
    LAST_NAME = (By.ID, "last-name")
    ZIP_CODE = (By.ID, "postal-code")
    CONTINUE_BTN = (By.ID, "continue")
    ERROR_MESSAGE = (By.CSS_SELECTOR, ".error-message-container")

    def enter_shipping_info(self, first_name, last_name, zip_code):
        """填写配送信息"""
        self.driver.find_element(*self.FIRST_NAME).send_keys(first_name)
        self.driver.find_element(*self.LAST_NAME).send_keys(last_name)
        self.driver.find_element(*self.ZIP_CODE).send_keys(zip_code)

    def click_continue(self):
        """点击继续按钮"""
        self.driver.find_element(*self.CONTINUE_BTN).click()

    def get_error_message(self):
        """获取错误提示文本"""
        return self.wait.until(
            EC.visibility_of_element_located(self.ERROR_MESSAGE)
        ).text
```

```py
# tests/test_checkout.py
import allure
import pytest
from pages.login_page import LoginPage
from pages.product_page import ProductPage
from pages.cart_page import CartPage
from pages.checkout_page import CheckoutPage
from pages.overview_page import OverviewPage


@allure.feature("结账流程")
@pytest.mark.usefixtures("driver")
class TestCheckout:
    @pytest.fixture(autouse=True)
    def setup(self, driver):
        """初始化：登录并添加商品到购物车"""
        self.driver = driver
        self.login_page = LoginPage(driver)
        self.product_page = ProductPage(driver)
        self.cart_page = CartPage(driver)
        self.checkout_page = CheckoutPage(driver)
        self.overview_page = OverviewPage(driver)

        # 登录并添加商品
        self.login_page.login("standard_user", "secret_sauce")
        self.product_page.add_product_to_cart(0)
        self.product_page.open_cart()
        self.cart_page.proceed_to_checkout()

    @allure.story("表单验证 - 必填字段为空")
    def test_empty_form_submission(self):
        # 表单为空
        with allure.step("提交空表单"):
            self.checkout_page.click_continue()
            assert "First Name is required" in self.checkout_page.get_error_message()
        # last name 为空
        with allure.step("提交空Last Name"):
            self.checkout_page.enter_shipping_info("test","","")
            self.checkout_page.click_continue()
            assert "Last Name is required" in self.checkout_page.get_error_message()
        # zip code 为空
        with allure.step("zip code"):
            self.checkout_page.enter_shipping_info("test","test","")
            self.checkout_page.click_continue()
            assert "Postal Code is required" in self.checkout_page.get_error_message()

    @allure.story("成功提交订单")
    def test_successful_checkout(self):
        with allure.step("填写有效信息并提交"):
            self.checkout_page.enter_shipping_info("John", "Doe", "12345")
            self.checkout_page.click_continue()
            # 验证跳转到订单概览页
            assert "checkout-step-two" in self.driver.current_url

        with allure.step("完成订单"):
            self.overview_page.click_finish()
            assert "checkout-complete" in self.driver.current_url
            assert "Thank you for your order!" in self.driver.page_source

    @allure.story("验证价格计算")
    def test_price_calculation(self):
        self.checkout_page.enter_shipping_info("John", "Doe", "12345")
        self.checkout_page.click_continue()

        # 获取商品总价和税费
        item_total = float(
            self.overview_page.driver.find_element(*self.overview_page.ITEM_TOTAL).text.replace("Item total: $", ""))
        tax = float(self.overview_page.driver.find_element(*self.overview_page.TAX).text.replace("Tax: $", ""))
        total = self.overview_page.get_total_price()

        # 验证总价计算正确
        assert total == round(item_total + tax, 2), "总价计算错误"
```

5.概述页面

```py
# pages/overview_page.py
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait

class OverviewPage:
    def __init__(self, driver):
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    # 元素定位器
    ITEM_TOTAL = (By.CLASS_NAME, "summary_subtotal_label")
    TAX = (By.CLASS_NAME, "summary_tax_label")
    TOTAL = (By.CLASS_NAME, "summary_total_label")
    FINISH_BTN = (By.ID, "finish")
    CANCEL_BTN = (By.ID, "cancel")

    def get_total_price(self):
        """解析总价（数值）"""
        total_text = self.driver.find_element(*self.TOTAL).text
        return float(total_text.replace("Total: $", ""))

    def click_finish(self):
        """完成订单"""
        self.driver.find_element(*self.FINISH_BTN).click()

    def click_cancel(self):
        """取消订单"""
        self.driver.find_element(*self.CANCEL_BTN).click()
```

#### 5.conftest

```py
from selenium import webdriver
import pytest
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.firefox.service import Service as FirefoxService
from selenium.webdriver.edge.service import Service as EdgeService
from webdriver_manager.chrome import ChromeDriverManager
from webdriver_manager.firefox import GeckoDriverManager
from webdriver_manager.microsoft import EdgeChromiumDriverManager

def pytest_addoption(parser):
    parser.addoption(
        "--browser",          # 命令行参数名称（以 `--` 开头）
        action="store",       # 存储参数值（默认动作）
        default="chrome",     # 未指定参数时的默认值
        help="chrome/firefox/edge"  # 在 `pytest --help` 中显示的帮助信息
    )

@pytest.fixture(scope="function")
def driver(request):
    browser = request.config.getoption("--browser").lower()

    if browser == "chrome":
        options = webdriver.ChromeOptions()
        options.add_argument("--headless")  # 无头模式可选
        chrome_service = ChromeService(ChromeDriverManager().install())
        driver = webdriver.Chrome(service=chrome_service, options=options)
    elif browser == "firefox":
        firefox_service = FirefoxService(GeckoDriverManager().install())
        driver = webdriver.Firefox(service=firefox_service)
    elif browser == "edge":
        edge_service = EdgeService(EdgeChromiumDriverManager().install())
        driver = webdriver.Edge(service=edge_service)
    else:
        raise ValueError(f"Unsupported browser: {browser}")
    driver.get("https://saucedemo.com/")
    yield driver
    driver.quit()
```

>**生成Allure报告**
>
>* `pytest --alluredir=./allure-results `   *运行所有测试并生成Allure数据*
>* `allure serve ./allure-results`    *生成HTML报告*
>
>* `allure generate ./allure-results -o ./allure-report --clean `    *生成静态报告*

>问题：
>
>* 没有生成跨浏览器的Allure报告



>
>
>1.并行测试执行
>
>```bash
># 安装插件
>pip install pytest-xdist
># 安装工具包
>pip install webdriver-manager
>
># 并行执行 Chrome 和 Firefox
>pytest tests/ --browser=chrome --numprocesses=2
>pytest tests/ --browser=edge --numprocesses=2
>```

**关键参数解释**：

|  **参数**   |                           **说明**                           |
| :---------: | :----------------------------------------------------------: |
| `--browser` | 命令行参数名，使用时需写成 `--browser=chrome` 或 `--browser firefox`。 |
|  `action`   | 参数处理方式：`store`（存储值）、`store_true`（标记布尔值）等。 |
|  `default`  | 未指定参数时的默认值。**eg:**`pytest`  *# 使用默认值 "chrome"* |
|   `help`    |      在帮助文档中显示的描述信息。**eg:**`pytest --help`      |

### 四、其他

#### 4.1.`pytest debug`

### **1. 直接使用 `pytest` 的调试模式**

在命令中添加 `--pdb` 参数，当测试失败时自动进入 `pdb` 调试控制台：

```bash
pytest -vs --pdb
```

- **效果**：测试失败时会暂停并进入 `pdb` 调试环境。

- 调试命令示例

  ```bash
  (Pdb) print(variable_name)  # 查看变量值
  (Pdb) list                  # 查看当前代码上下文
  (Pdb) continue             # 继续执行后续测试
  ```

