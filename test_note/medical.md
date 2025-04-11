## 一、Python环境

```cmd
# 创建虚拟环境（隔离依赖）
python -m venv venv  

# 激活虚拟环境（Windows）
venv\Scripts\activate  
# Mac/Linux用：source venv/bin/activate

# 安装必备库
pip install requests pytest allure-pytest pymysql
```

## 二、搭建框架结构

medical_api_test/
├── .gitignore         # 忽略文件（复制内容：https://gitignore.io/api/python）
├── requirements.txt   # 依赖清单（内容：requests==2.31.0）
├── config/
│   └── settings.py    # 配置文件
├── utils/
│   ├── http_client.py # HTTP工具
│   └── db_handler.py  # 数据库工具
└── test_cases/
    └── \_\_init\_\_.py  # 空文件，告诉Python这个目录是一个包，可以用来导入模块
    └── test_bloodpressure.py  # 测试用例
    └── test_demo.py  # 运行测试

>**区别：**
>`__init__.py ` 告诉Python这个目录是一个包，可以用来导入模块
>类的`__init__`方法是用来初始化实例的

### 2.1 config

```py
# config/settings.py
BASE_URL = "http://127.0.0.1:3000"  # 测试用模拟地址
DB_CONFIG = {  # 连接mysql配置
    "host": "localhost",
    "user": "root",
    "password": "chen",
    "database": "medical_db"
}
```



```py
# config/logging.py
import logging
import os
def setup_logging():
    # 原始拼接路径
    raw_path = os.path.join(os.path.dirname(__file__), "..", "app.log")
    # 规范化处理
    normalized_path = os.path.normpath(raw_path)
    # print(f"日志文件路径: {normalized_path}")
    logging.basicConfig(
        filename=normalized_path,
        level=logging.ERROR,
        format="%(asctime)s - %(levelname)s - %(message)s",
        force=True
    )
```

> **为什么需要强制覆盖？**
> **（1）第三方库或框架的干扰**
> **场景**：某些框架（如 pytest、Django）在启动时会默认初始化日志系统（例如添加 StreamHandler）。
> **问题**：用户自定义的日志配置（如写入文件）会被忽略，因为框架的配置已经生效。
> **解决**：强制覆盖以清除框架的默认配置。
> **（2）多次初始化需求**
> **场景**：项目中需要多次动态调整日志配置（例如根据环境切换日志级别）。
> **问题**：默认情况下，只有第一次配置会生效。
> **解决**：每次调整配置时强制覆盖
>
> **logging.basicConfig() 的默认限制**
> Python 的 `logging.basicConfig() `默认仅在 日志系统完全未被初始化时生效。
> 如果其他代码（如框架或第三方库）已添加了处理器（Handler）或设置了日志级别，自定义配置会被忽略
>
> **Python 3.8+**
> `logging.basicConfig(force=True)`
>
> **Python <3.8**
> 手动清除现有处理器
>
> ```py
> def setup_logging():
> # 获取根记录器
> root_logger = logging.getLogger()
> 
> # 移除所有现有处理器
> for handler in root_logger.handlers[:]:
>   root_logger.removeHandler(handler)
> ```
>
> **注意事项**
> **性能影响**：频繁强制覆盖可能影响性能，建议仅在初始化阶段使用。
> **兼容性**：force=True 仅适用于 Python 3.8+，旧版本需手动处理。
> **副作用**：可能清除其他模块添加的处理器（如第三方库的日志配置），需谨慎使用

### 2.2 utils

```py
# utils/http_client.py
import requests
import logging
from config.settings import BASE_URL
from config.logging import setup_logging
# 初始化日志配置
setup_logging()
import logging
logger = logging.getLogger(__name__)
class HttpClient:
    def post(self, endpoint, data):
        url = f"{BASE_URL}{endpoint}"
        try:
            return requests.post(url, json=data, timeout=3)
        except requests.exceptions.Timeout as e:
            logger.error(f"登录请求超时: {e}")
            return None
        except requests.exceptions.RequestException as e:
            logger.error(f"登录请求失败: {e}")
            return None
```

> **self的作用**
> 在类方法中，self是一个约定俗成的名称，表示类的实例对象本身。它的作用包括：
>
> **访问实例属性**：通过self.属性名可以访问该实例的属性和方法。
> **调用其他方法**：例如在post方法中调用self.another_method()。
> **维护对象状态**：在面向对象编程中，类的不同实例可能有不同的状态，self使得每个实例能独立操作自己的数据
>
> **注意**：self只是一个命名约定，理论上可以用其他名称（如this），但强烈建议遵循惯例使用self

>`json=data`: 自动将 `data` 序列化为 JSON 并设置请求头 `Content-Type: application/json`

> **捕获的异常类型**
> `requests.exceptions.RequestException` 是 requests 库中所有异常的 基类（父类），包括但不限于：
>
> * **连接失败**（如对方服务器未启动、域名不存在）→ ConnectionError
>
> * **请求超时**（如连接或响应超时）→ Timeout
>
> * **HTTP 错误**（如 404 页面不存在、500 服务器错误）→ HTTPError
>
> * **SSL 证书错误** → SSLError
>
> * **无效 URL** → InvalidURL
>
> * **其他网络相关的异常**
>
>   通过捕获 `RequestException`，代码能够覆盖绝大多数网络请求可能出现的错误

### 2.3 test_case

```py
# 运行测试
# 临时创建 test_case/test_demo.py， 验证框架
from utils.http_client import HttpClient

def test_demo():
    client = HttpClient()
    r = client.post("/login", {"username": "admin","password": "admin"})
    assert r.status_code == 200
```

2.4 模拟真实接口

2.4.1 使用免费API模拟工具：

- 访问 https://mockoon.com/ 下载安装

- 创建模拟接口：

  ```py
  POST /api/bloodpressure  
  响应示例：{"code":200, "message":"success"}
  ```

