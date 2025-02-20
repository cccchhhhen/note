1. 初始化本地仓库：  
    * 打开终端，进入项目文件夹。  
    * 运行 `git init` 初始化本地仓库。
2. 添加远程仓库：
    * 在 GitHub 上创建新仓库，复制仓库 URL。
    * 运行` git remote add origin <仓库URL>` 添加远程仓库。
    
3. 添加并提交文件：
    * 运行 `git add .` 添加所有文件（包括文件夹）。
    * 运行 `git commit -m "Initial commit"` 提交更改。

4. 推送文件：
    * 运行 `git push -u origin main`（或 master）将文件推送到 GitHub。
    
    
    
    
**报错：**
>error: src refspec main does not match any
error: failed to push some refs to 'https://github.com/cccchhhhen/html_css_js_project.git'

这个错误通常是因为本地仓库的分支名称与远程仓库的分支名称不匹配。**Git** 默认使用 **master** 作为主分支，但 **GitHub** 现在默认使用 **main** 作为主分支。因此，当你尝试推送时，可能会出现分支名称不匹配的问题。

1. 检查本地分支名称 `git branch`
    * 如果显示 * master，说明本地分支是 master。
    * 如果显示 * main，说明本地分支是 main
2. 重命名本地分支（如果需要）  
    * 如果本地分支是 master，而远程仓库使用的是 main，你需要将本地分支重命名为 main  
    `git branch -m master main`  
3. 推送本地分支到远程仓库  
    * 运行以下命令，将本地分支推送到远程仓库  
    `git push -u origin main`
4. 如果远程仓库没有 main 分支
    * 如果远程仓库还没有 main 分支，GitHub 会自动创建它。
    * 如果远程仓库已经有 main 分支，确保本地分支与远程分支同步  
    `git pull origin main`
    
window
通过`git commit`进入编辑器，通过`:wq`退出

