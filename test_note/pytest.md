## 一、安装

`pip install -U pytest` （U最新版）

验证 `pytest --version`

检查本地 Chrome 版本：浏览器地址栏输入 `chrome://version`，查看版本号（如 `120.0.6099.71`）

#### **常用国内镜像源**

- 清华大学：`https://pypi.tuna.tsinghua.edu.cn/simple`

- 阿里云：`https://mirrors.aliyun.com/pypi/simple`

- 豆瓣：`https://pypi.douban.com/simple`

- ```cmd
  pip install pytest -i https://pypi.tuna.tsinghua.edu.cn/simple
  ```

#### [**pytest文档**](https://docs.pytest.org/en/latest/contents.html)

## 二、pytest框架的约束

### 2.1  `pytest` 的命名规则

> 1）模块名（py文件、测试⽂件）应以 "test_" 开头或以 "_test" 结尾
> 例如： test_login.py 或 user_test.py
> 2）测试类（class）必须以Test开头，并且不能带init方法，类里的方法必须以test_开头_
> _如： class TestLogin
> 3）测试用例（函数）必须以test_开头
> 例如： def test_login_success(): 或 def test_add_product_to_cart():



## 三、pytest的运行方式

**生成测试报告**

`pip install pytest-html`

`pytest --html=report.html`

**使用插件 `pytest-cov` 生成覆盖率报告：**

`pip install pytest-cov`
`pytest --cov=my_project`

eg: `pytest test_0409.py --html=./report/report.html` 

**失败重试**

安装 `pytest-rerunfailures` 插件，在失败时自动重试：

`pip install pytest-rerunfailures`
`pytest --reruns 3`

`pytest --version`

**生成allure报告**

`pip install allure-pytest`

`pytest --alluredir=./allure-results`

` allure serve ./allure-results`

3.2 `pytest` 运行时携带的参数

**-s** ：

表示开启终端交互，其作⽤是可以让打印的内容输出显示在终端中，或者可以在终端中与⽤例中的输⼊操作进⾏交互

**-v**：

表示详细输出更详细的输出，包括每个测试⽤例的详细结果和其他相关信息，例如测试⽤例所在的模块、⽂件路径等

test/test_lesson1.py

```py
#主体例子1：
def test_case():
    print("用例被运行")


#主体例子2：烤鸭1.0
def test_duck():
    print("-----烤鸭店利润计算器开始⼯作------")
    price1 = int(input("请输⼊烤鸭的进货价：")) # input传递来的值，都是str
    price2 = int(input("请输⼊烤鸭的售卖价："))
    num = int(input("请输⼊今天卖出的烤鸭数量："))
    result = (price2 - price1) * num
    print("今天的烤鸭利润是{}元".format(result))
```

test/main.py

```py
#用例执行语句：
import pytest
pytest.main(["-s"])
```

控制台执行

`python .\main.py`

或者直接 `pytest`

**问题：**直接通过 `pytest` 可能找不到目录

**解决：**①添加 `__init__.py` 空文件

②创建`pytest`配置文件

```ini
# 创建 pytest.ini 文件
[pytest]
pythonpath = .
# 或当使用src目录结构时
pythonpath = src/
```


除了用main文件运行用例，还可以在Terminal终端用命令来执行用例：

```py
● 终端输入pytest：同等于在main文件的pytest.main([])
● 终端输入pytest -s：同等于在main文件的pytest.main(["-s"])
● 终端输入pytest -s -v：同等于在main文件的pytest.main(["-s","-v"])；终端的空格 = 逗号
● 终端输入pytest -sv：同等于在main文件的pytest.main(["-sv"]); -s -v的缩写
● 终端输入pytest -vs：同等于在main文件的pytest.main(["-vs"]); -s -v的缩写
-------------->>>>
● 指定用例运行：(失败了？？？)
终端输入pytest test_01.py::Test01::test001 -sv：同等于在main文件的pytest.main(["test_01.py::Test01::test001", "-vs"])；详解看下面指定运行介绍
```

3.3 pytest指定运⾏⽤例

① 指定运行其中一个模块的用例：

```cmd
pytest 模块名 [-s:显示输入内容]
```

② 指定运行某个模块中的某个类：

```cmd
pytest 模块名::函数名/类名  #（双冒号表示模块中的下一层）
```

③ 指定运行某个模块中的某个类下面的某个用例：

```cmd
pytest 模块名::函数名::类名
```

④ 指定文件夹运行用例：

```cmd
pytest.main(["文件名","-sv"])
```

指定文件夹下的子级文件的用例运行：

```cmd
pytest.main(["文件名/子文件夹名","-sv"])
```

指定文件夹下 -> 子级文件 - > 模块中的某个用例运行：

```cmd
pytest.main(["文件名/子文件夹名/模块名::用例名","-sv"])
```

⑤pytest运⽤-k筛选⽤例

> 筛选用例，一般以用例名作为筛选条件，配合筛选关键词“-k”使用即可

test\test_case\test_demo.py

```py
def test_0412():
    print('test_0412')

def test_0411():
    print('test_0411')

```

```py
pytest.main(["-k","041","-vs"])  # main文件的写法
pytest -k 041 -vs                # 终端命令行的写法
```

执行`python main.py`

```cmd
collected 4 items / 2 deselected / 2 selected         
test_case/test_demo.py::test_0412 test_0412
PASSED
test_case/test_demo.py::test_0411 test_0411
PASSED
```

控制台执行

`python .\main.py`

3.4 pytest的前置和后置处理

在pytest 中，可以使⽤ setup 和 teardown 函数来定义测试⽤例的前置和后置操作。

❶ **setup** 函数：它会在每个测试⽤例执⾏之前运⾏，⽤于设置测试环境和准备测试数据。

❷ **teardown** 函数：它会在每个测试⽤例执⾏之后运⾏，⽤于清理测试环境和资源。

ps：

* 代码中尽可能前置、后置写统一，要么都带上module，要么都不写

* setup、teardown 与setup_module、teardown_module同时出现，会导致前置和后置竞争，导致可能其中一个不会在终端显示出来。

>在 pytest 中，setup/teardown 是旧版 xUnit 风格的钩子函数，但它们的 命名必须严格符合规则 才能被自动调用：
>
>**模块级**：setup_module() / teardown_module()（整个测试文件执行前后运行一次） 文件的前后
>
>```py
>@pytest.fixture(autouse=True,scope="module")
>```
>
>**函数级**：setup_function() / teardown_function()（每个测试函数执行前后运行） 非类的每个函数前后
>
>```py
>def setup_function():
>def teardown_function():
>```
>
>```py
>@pytest.fixture(autouse=True,scope="function")
>def setup_teardown():
>print("执行前置操作")
>yield # yield 前的代码是前置操作，yield 后的代码是后置操作
>print("执行后置操作")
>```
>
>**类级**：setup_class() / teardown_class()（在测试类中，整个类执行前后运行一次）每个class前后
>
>```py
>class TestClass:
>def setup_class([self]):
>def teardown_class([self]):
>```
>
>**方法级**：setup_method() / teardown_method()（在测试类中，每个测试方法执行前后运行) 类中每个def前后
>
>```py
>class TestClass:
>def setup_method(self, method):
># 方法级别的前置操作
>print("执行方法级别的前置操作")
>
>def teardown_method(self, method):
>  # 方法级别的后置操作
>   print("执行方法级别的后置操作")
>```
>`scope="module"`：整个测试模块执行一次。
>`scope="session"`：整个测试（文件）会话执行一次。
>`scope = "class"`：除了声明的类，函数也同样会执行
>`scppe = "function"` （默认值）：每个测试函数都会调⽤⼀次 fixture(与前置一样)

3.5 pytest的标签使⽤

在Pytest中，标签（也称为标记）是⼀种⽤于对测试⽤例进⾏分类或分组的机制。

通过为测试⽤例添加标签，可以在运⾏测试时选择性地执⾏特定标签的测试⽤例，从⽽ ⽅便地控制测试的范围和⽬标。

**可以理解为：标签也是用来做过滤的一种方式。**

Pytest的标签功能基于装饰器来实现。

可以使⽤ `@pytest.mark` 装饰器为测试函数或测试类添加标签。

常⻅的⽤法是在测试函数或测试类上⽅添加装饰器，并指定相应的标签。

mark后不同关键字的含义：

`@pytest.mark.skip`：标记测试函数为跳过测试，并提供跳过的原因。这在你想要暂时跳过某些测试的情况下很有用，比如某个功能尚未实现或者有缺陷需要修复。

`@pytest.mark.xfail`：标记预期失败的测试。这表示你预期该测试会失败，但你想要运行它并收集结果。你可以提供失败的原因。

`@pytest.mark.parametrize`：使用参数化标记来标记测试函数，并提供参数化的值。这样测试函数将会根据提供的参数值运行多次，每次运行时使用不同的参数值。

`@pytest.mark.smoke`：通常用于标记快速运行、覆盖基本功能、且对整体系统影响较小的测试。Smoke 测试旨在快速验证系统的基本功能是否正常，通常在每次代码提交后都会运行，以便快速发现潜在的问题。

`@pytest.mark.slow`： 通常用于标记运行较慢、覆盖范围较广、对整体系统影响较大的测试。Slow 测试可能包括集成测试、端到端测试、性能测试等，需要更长的时间来执行。

**自定义标签：**

@pytest.mark.任意标签内容：但是不能有中文、不能以数字开头；自定义标签mu

**注册自定义标记**

项目根目录`pytest.ini`

```ini
[pytest]
markers =
    smoke: 冒烟测试
```

**查看已注册标记**

`pytest --markers`

```py
# 例子
import pytest

@pytest.mark.smoke
def test_login():
    # 测试登录功能
    assert True

@pytest.mark.regression
def test_registration():
    # 测试注册功能
    assert True
```

smoke执行

```py
pytest -vs -m smoke                   # 终端命令行的写法
pytest.main(["-vs", "-m", "smoke"])   # main文件的写法
```

**标签筛选逻辑**

```py
逻辑分为四种：是、非、或、和
是：pytest.main(["-vs", "-m", "hailey"])  -->> pytest -m 'hailey' -vs
非：pytest.main(["-m", "not hailey", "-vs"])  -->> pytest -m 'not hailey' -vs

或：pytest.main(["-vs", "-m", "hailey or smoke"])  
        终端：-->> pytest -m 'hailey or smoke' -vs
和：pytest.main(["-vs", "-m", "hailey and smoke"])  
        终端：-->> pytest -m 'hailey and smoke' -vs
    同时打有两个标签的，如
    @pytest.mark.smoke
    @pytest.mark.hailey
```

3.6 pytest的conftest的使⽤

conftest.py 是⼀个特殊的⽂件，⽤于管理测试⽤例中 需要通⽤数据传递

3.6.1 pytest.fixture装饰器

**使⽤ pytest.fixture ，目的是为了更⽅便地管理测试⽤例的前置条件和后置操 作，实现测试数据的共享和代码的重⽤，从⽽使测试代码更加简洁、可维护和可扩展**

3.6.2 pytest.fixture的参数

❶ **scope** ：指定 fixture 的作⽤域。可以设置为以下⼏个值之⼀：

​    ● function （默认值）：每个测试函数都会调⽤⼀次 fixture(与前置一样)

​    ● class ：每个测试用例的类都会调⽤⼀次 fixture。

​    ● module ：每个测试模块都会调⽤⼀次 fixture。

​    ● session：整个测试会话只会调⽤⼀次 fixture。

​    ● yield：每个测试函数都会调⽤⼀次 fixture(与后置一样)

❷ **params** ：参数化 fixture，可以根据不同的参数值⽣成多个独⽴的 fixture 实例。 可以传递⼀个可迭代对象，每个元素都会作为参数值调⽤ fixture。

参数化夹具（使用`params` 和 `request`）

```py
import pytest


@pytest.fixture(params=[1, 2, 3])
def param_fixture(request):
    return request.param # 不可以使用其他属性名 如request.data

def test_param_fixture(param_fixture):
    assert param_fixture in [1, 2, 3]
```

**总结：**params 和 request 是 pytest 框架的固定设计，用于实现夹具的参数化

**对比：**参数化测试函数（使用 @pytest.mark.parametrize）

```py
@pytest.mark.parametrize("value", [1, 2, 3])
def test_example(value):
    assert value > 0
```



❸ **autouse** ：⾃动使⽤ fixture，⽆需在测试函数中显式声明使⽤该 fixture。可以 设置为 True 或 False 。

❹ **name** ：为 fixture 指定⼀个⾃定义名称，⽤于在测试报告和⽇志中标识 fixture。

❺ **其他参数**：可以根据需要添加其他⾃定义参数，供 fixture 函数使⽤。

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

