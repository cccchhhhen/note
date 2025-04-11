## 一、安装

`pip install -U pytest`

验证 `pytest --version`

#### [**pytest文档**](https://docs.pytest.org/en/latest/contents.html)

## 二、pytest框架的约束

### 2.1  `pytest` 的命名规则

> 1）模块名（py文件）必须是以test_开头或者_test结尾
> 2）测试类（class）必须以Test开头，并且不能带init方法，类里的方法必须以test_开头
> 3）测试用例（函数）必须以test_开头

## 三、pytest的运行方式

下载 `pip install pytest-html`

`pytest test_0409.py --html=./report/report.html` 

## 四、虚拟环境

* 核心作用：隔离环境、精准管理依赖、避免冲突。
* 推荐场景：所有Python项目开发、团队协作、多版本测试。
* 最佳实践：每个项目单独使用一个虚拟环境。

操作示例
1. 创建虚拟环境
python -m venv myenv  # 创建名为 myenv 的虚拟环境
2. 激活环境
Windows：
myenv\Scripts\activate
macOS/Linux：
source myenv/bin/activate
3. 安装依赖
pip install django==2.2  # 仅在当前虚拟环境中生效
4. 导出依赖
pip freeze > requirements.txt
5. 退出环境
deactivate

五、构建 REST API 自动化测试工具

安装 Python 3.9+
从 Python 官网 下载并安装，确保勾选 Add Python to PATH。
创建虚拟环境（推荐）

`python -m venv venv
venv\Scripts\activate     # Windows`

注：运行which python或者where python看看路径是否指向虚拟环境目录(虚拟环境目录在前)

C:\Users\Administrator\AppData\Local\Programs\Python\Python39\workspace\venv\Scripts\python.exe
C:\Users\Administrator\AppData\Local\Programs\Python\Python39\python.exe

api_auto_test/
├── config/
│   ├── config.yaml       # 全局配置
│   └── test_data.yaml    # 测试数据
├── test_cases/
│   ├── test_auth.py      # 认证相关测试
│   └── test_user_api.py  # 用户接口测试
├── utils/
│   ├── api_client.py     # 封装 Requests
│   ├── logger.py         # 日志模块
│   └── assertions.py     # 自定义断言
├── conftest.py           # Pytest 全局配置
├── requirements.txt
└── README.md