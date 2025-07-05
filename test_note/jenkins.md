[官网](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu)

##### 1.根据官网安装

`User Handbook` -> `Installing Jenkins` -> `Linux` -> `Long Term Support release`

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

此时在本地是打不开的`http://ip:8080`

##### 2.检查jenkins服务状态

```bash
sudo systemctl status jenkins
```

结果为

```bash
chen@chen-virtual-machine:~$ sudo systemctl status jenkins
[sudo] chen 的密码：
× jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor prese>
     Active: failed (Result: exit-code) since Fri 2025-07-04 10:10:31 CST; 25mi>
    Process: 3620 ExecStart=/usr/bin/jenkins (code=exited, status=1/FAILURE)
   Main PID: 3620 (code=exited, status=1/FAILURE)
        CPU: 8ms

7月 04 10:10:31 chen-virtual-machine systemd[1]: jenkins.service: Scheduled res>
7月 04 10:10:31 chen-virtual-machine systemd[1]: Stopped Jenkins Continuous Int>
7月 04 10:10:31 chen-virtual-machine systemd[1]: jenkins.service: Start request>
7月 04 10:10:31 chen-virtual-machine systemd[1]: jenkins.service: Failed with r>
7月 04 10:10:31 chen-virtual-machine systemd[1]: Failed to start Jenkins Contin>
chen@chen-virtual-machine:~$ sudo systemctl start jenkins
Job for jenkins.service failed because the control process exited with error code.
See "systemctl status jenkins.service" and "journalctl -xeu jenkins.service" for details.

```

结果显示不全

通过`sudo systemctl status jenkins.service | less`发现是`java`问题

##### 3.确认`java`

```bash
java -version
# 或者
javac
```

发现`java` 没有安装

##### 4.修复 Java 环境

```bash
sudo apt install openjdk-11-jdk

# 确保使用正确的 JAVA_HOME
sudo sed -i 's|JAVA_HOME=.*|JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64|' /etc/default/jenkins
echo 'JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64' | sudo tee -a /etc/default/jenkins

# 检查 Java 路径
sudo update-alternatives --config java
```

##### 5.重置 Jenkins 目录权限

```bash
sudo chown -R jenkins:jenkins /var/lib/jenkins
sudo chown -R jenkins:jenkins /var/log/jenkins
sudo chown -R jenkins:jenkins /var/cache/jenkins
sudo chmod 755 /var/lib/jenkins
```

##### 6.检查并修复配置文件

```bash
# 检查配置文件有效性
sudo bash -c 'source /etc/default/jenkins && echo $JAVA_ARGS $JENKINS_ARGS'

# 重置为默认设置
sudo tee /etc/default/jenkins << 'EOF'
# defaults for jenkins continuous integration server

# pulled in from the init script; makes things easier.
NAME=jenkins

# location of java
JAVA_BIN=/usr/bin/java

# arguments to pass to java
JAVA_ARGS="-Djava.awt.headless=true"

# run jenkins as this user
JENKINS_USER=jenkins
JENKINS_GROUP=jenkins

# directory where the jenkins war file is
JENKINS_WAR="/usr/share/java/jenkins.war"

# jenkins home location
JENKINS_HOME="/var/lib/jenkins"

# set this to false to allow unsecured access over http
JENKINS_ENABLE_ACCESS_LOG="false"

# arguments to pass to jenkins
JENKINS_ARGS="--webroot=/var/cache/jenkins/war"

# port for HTTP connector (default 8080; disable with -1)
HTTP_PORT=8080
EOF
```

##### 7.手动运行以获取详细错误

```bash
# 停止服务
sudo systemctl stop jenkins

# 手动启动并查看详细错误
sudo -u jenkins java -jar /usr/share/java/jenkins.war
```

显示 Jenkins 启动时的具体错误信息

```bash
chen@chen-virtual-machine:~$ sudo -u jenkins java -jar /usr/share/java/jenkins.war
Running with Java 11 from /usr/lib/jvm/java-11-openjdk-amd64, which is older than the minimum required version (Java 17).
Supported Java versions are: [17, 21]
See https://jenkins.io/redirect/java-support/ for more information.
```

结果因为`java`版本错误

##### 8.安装 OpenJDK 17

```bash
sudo apt update
sudo apt install openjdk-17-jdk
```



##### 9.配置 Jenkins 使用 Java 17

```bash
# 找到 Java 17 的安装路径
sudo update-alternatives --config java
```
```bash
chen@chen-virtual-machine:~$ sudo update-alternatives --config java
有 2 个候选项可用于替换 java (提供 /usr/bin/java)。

  选择       路径                                       优先级  状态
------------------------------------------------------------
* 0            /usr/lib/jvm/java-17-openjdk-amd64/bin/java   1711      自动模式
  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java   1111      手动模式
  2            /usr/lib/jvm/java-17-openjdk-amd64/bin/java   1711      手动模式

要维持当前值[*]请按<回车键>，或者键入选择的编号：
```
```bash
# 设置 JAVA_HOME
JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
echo "JAVA_HOME=${JAVA_HOME}" | sudo tee -a /etc/default/jenkins

# 验证 Java 版本
sudo -u jenkins ${JAVA_HOME}/bin/java -version
```

```bash
chen@chen-virtual-machine:~$ sudo -u jenkins ${JAVA_HOME}/bin/java -version
openjdk version "17.0.15" 2025-04-15
OpenJDK Runtime Environment (build 17.0.15+6-Ubuntu-0ubuntu122.04)
OpenJDK 64-Bit Server VM (build 17.0.15+6-Ubuntu-0ubuntu122.04, mixed mode, sharing)
```

##### 10.完全重启 Jenkins 服务

```bash
sudo systemctl restart jenkins
```

```bash
chen@chen-virtual-machine:~$ sudo systemctl restart jenkins
chen@chen-virtual-machine:~$ sudo systemctl status jenkins
● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor prese>
     Active: active (running) since Fri 2025-07-04 10:59:38 CST; 5min ago
   Main PID: 6497 (java)
      Tasks: 48 (limit: 4545)
     Memory: 691.6M
        CPU: 23.543s
     CGroup: /system.slice/jenkins.service
             └─6497 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java>

7月 04 10:59:30 chen-virtual-machine jenkins[6497]: ee70bc68d3c445229abdaabd6e1>
7月 04 10:59:30 chen-virtual-machine jenkins[6497]: This may also be found at: >
7月 04 10:59:30 chen-virtual-machine jenkins[6497]: ***************************>
7月 04 10:59:30 chen-virtual-machine jenkins[6497]: ***************************>
7月 04 10:59:30 chen-virtual-machine jenkins[6497]: ***************************>
7月 04 10:59:38 chen-virtual-machine jenkins[6497]: 2025-07-04 02:59:38.551+000>
7月 04 10:59:38 chen-virtual-machine jenkins[6497]: 2025-07-04 02:59:38.564+000>
7月 04 10:59:38 chen-virtual-machine systemd[1]: Started Jenkins Continuous Int>
7月 04 10:59:41 chen-virtual-machine jenkins[6497]: 2025-07-04 02:59:41.555+000>
7月 04 10:59:41 chen-virtual-machine jenkins[6497]: 2025-07-04 02:59:41.556+000>
```

##### 11.`Jenkins`使用

###### 11.1登录

本地访问`http:192.168.17.128:8080`

获取密码

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

```bash
chen@chen-virtual-machine:~$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
[sudo] chen 的密码：
ee70bc68d3c445229abdaabd6e1e00bf
```

新手入门：安装了推荐插件

Folders
Timestamper
Pipeline
Git
LDAP
Localization: Chinese(Simplified)
OWASP Markup Formatter
Workspace Cleanup
GitHub Branch Source
SSH Build Agents
Email Extension
Build Timeout
Ant
Pipeline: GitHub Groovy Libraries
Matrix Authorization Strategy
Mailer
Credentials Binding
Gradle
Pipeline Graph View
PAM Authentication
Dark Theme

实例配置

`Jenkins URL: http://192.168.17.128:8080/`

###### 11.2配置

`Dashboard -> Manage Jenkins -> Tools`

**A.** 集成`jdk`

* 别名：`jdk17`

* `JAVA_HOME`：`/usr/lib/jvm/java-17-openjdk-amd64`

  ```bash
  # 找到java路径 (手动定位)
  chen@chen-virtual-machine:~$ which java
  /usr/bin/java
  
  chen@chen-virtual-machine:~$ ls -l /usr/bin/java
  lrwxrwxrwx 1 root root 22  7月  4 10:43 /usr/bin/java -> /etc/alternatives/java
  
  chen@chen-virtual-machine:~$ ls -l /etc/alternatives/java
  lrwxrwxrwx 1 root root 43  7月  4 10:57 /etc/alternatives/java -> /usr/lib/jvm/java-17-openjdk-amd64/bin/java
  ```

  

**B.** `Git`

`jenkins`服务器安装了`git`这里就默认有



**C.** 使用`jenkins`拉取`git`中代码

1. 新建`Item`
2. 输入一个任务名称：`JenkinsTest`（建议与仓库名称一致）
3. 选择`Freestyle Project`
4. 点击确定
5. 创建完毕后，找到源码管理，选择`git`创建
6. 填写`respositories URL`和`Credential`，账号密码是`github`的账号密码
7. 点击`save`
8. 此时`jenkins`无法自动拉取代码，手动构建`Buils Now`，控制台输出`Finish Success`表示成功

**D.** 集成`Maven`

> **Maven vs Python 项目配置对比**
>
> |     功能     |           `Java/Maven` 项目            |          `Python` 项目           |
> | :----------: | :------------------------------------: | :------------------------------: |
> | **代码拉取** | 在 `Source Code Management` 配置 `Git` |         **完全相同配置**         |
> | **依赖安装** |      `Maven` 自动下载 (`pom.xml`)      | `pip` 从 `requirements.txt` 安装 |
> | **构建步骤** |          `mvn clean package`           |          **不需要构建**          |
> | **测试执行** |               `mvn test`               |             `pytest`             |
> | **结果报告** |           JUnit 插件收集报告           |     相同插件收集 pytest 报告     |

```bash
sudo apt update
sudo apt install maven -y
mvn -version

# 获取maven路径
which mvn
```

1. 新建`Item`
2. 输入一个任务名称：`JenkinsTestMaven`
3. 选择`构建一个maven项目`(如果是`python`项目，选择`Freestyle Project`就可以)
4. 点击确定
5. 创建完毕后，找到源码管理，选择`git`创建
6. 填写`respositories URL`和`Credential`，账号密码是`gitee`的账号密码
7. `Triggers ->`勾选`Gitee webhook`触发构建，需要在 `Gitee webhook` 中填写 URL: http://192.168.17.128:8080/gitee-project/JenkinsTestMaven
8. 生成`Gitee WebHook` 密码，`589a1ca228ab32aadd121169d4ec4b06`
9. 点击`save`
10. 配置内网穿透：
    * `gitee`仓库的代码，默认**`gitee`不允许内网拉取**
    * 这里使用`cpolar`，新建一个隧道
      * 隧道名称：`jenkins`
      * 协议：`http`
      * 本地地址：`http://192.168.17.128:8080`
      * 域名类型：随机域名
      * 地区：`China Top`
      * 激活
    * 在线隧道列表中的公网地址：`http://2ea48611.r27.cpolar.top`
11. 在`gitee`仓库项目，点击"管理" -> "仓库挂件/`WebHooks`"
    * `URL：http://2ea48611.r27.cpolar.top/gitee-project/JenkinsTestMaven`
    * `WebHook `密码/签名密钥：`589a1ca228ab32aadd121169d4ec4b06`
12. 此时只要往`gitee`上提交代码，`jenkins`可以自动拉取代码并构建

注：maven 与是否自动拉取`git`项目无关



##### 12.`jenkins`拉取代码后的操作

**A.** Jenkins的工作空间（Workspace）

* **定义**：当Jenkins任务运行时，会为每个任务分配一个**独立的工作空间目录**，所有代码拉取和构建操作都在此目录进行。
* **位置**：通常位于Jenkins主目录下的`workspace`文件夹中，具体路径为`/var/lib/jenkins/workspace/<任务名称>`

```bash
chen@chen-virtual-machine:~$ ls /var/lib/jenkins/workspace
JenkinsTest  JenkinsTestMaven  JenkinsTestMaven@tmp  JenkinsTest@tmp
```

**B.** 构建时`Python`虚拟环境

在配置中 -> `Build Steps` -> `Execute shell`

```bash	
#!/bin/bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

pytest
```

查看`.venv`文件 ： `ll`

控制台输出：

```bash
The virtual environment was not created successfully because ensurepip is not
available.  On Debian/Ubuntu systems, you need to install the python3-venv
package using the following command.

    apt install python3.10-venv

You may need to use sudo with that command.  After installing the python3-venv
package, recreate your virtual environment.

Failing command: /var/lib/jenkins/workspace/JenkinsTest/.venv/bin/python3
```

因此需要先安装`venv`：`sudo apt install python3.10-venv`

改进：判断虚拟环境是否已经创建

```bash
#!/bin/bash
if [ -d ".venv" ];then
	echo "虚拟环境已创建"
else
	echo "虚拟环境未创建"
    python3 -m venv .venv
    source .venv/bin/activate
fi

pip install -r requirements.txt

pytest
```

