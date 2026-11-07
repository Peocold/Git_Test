# Git 学习记录

## 新建仓库并用Git上传

0. **配置身份信息（一次性）**
```bash
git config --global user.name  "用户名"
git config --global user.email "邮箱"
# --global的意思是全局配置，对当前用户的所有git仓库生效
```
另外将本地仓推送到github有两种认证方式：
- HTTPS：凭证用`PAT`（在`Settings`→`Developer settings`→`Personal access tokens`生成）
- SSH：用一对密钥，需要把公钥登记到github，配置方式如下
```bash
ssh-keygen -t ed25519 -C "注册github用的邮箱"
# 回车三次
# 启动ssh-agent
eval "$(ssh-agent -s)"
# 添加私钥到代理
ssh-add ~/.ssh/id_ed25519
# 打印公钥，然后复制输出结果
cat ~/.ssh/id_ed25519.pub
```
- 在github中点击右上角头像点进`Settings`
- 左侧菜单拉到最下面，找到`SSH and GPG keys`，点击`New SSH key`
- 在`Title`处起个名，在`Key`处粘贴复制到的公钥，再点`Add SSH key`即可
1. **在github新建一个仓库并复制仓库地址（假如配置了ssh就选ssh的）**

建仓时不要勾选`Add a README file / .gitignore / license`，否则远程已有提交用下文提到的push指令会被拒，这时需要先用`git pull --rebase origin main`拉取（fetch）并合并（merge）才能push。

2. **在项目文件夹下运行终端，在终端中初始化本地仓库**
```bash
git init
```
3. **配置云端仓库地址**
```bash
git remote add origin <仓库地址>
# 远程名习惯设为origin
```
想看下远程地址执行这行
```bash
git remote -v
# -v即--verbose，，意思是冗长、详细
```
4. **配置gitignore**

暂存前须写好`.gitignore`（忽略版本追踪），以避免不必要的文件（日志、缓存等）上传云端。gitignore的书写规则如下:
|符号|作用/规则|
|---|---|
| 空行 |空行不匹配任何文件，因此常用作分隔符（以方便阅读）|
| # |注释|
| \ |表示转义（如对 a!bc.txt，需改为 a\ !bc.txt）|
| * |可以匹配任何字符（零或多次），不可匹配 / |
| ? |可以匹配任何字符（1次），不可匹配 / |
| / |用于分隔目录|
| ! |原先被排除的文件，使用 ! 模式后该文件将会重新被包含。但如果了该文件的父级目录被忽略了，那么使用 ! 也不会再次被包含|
| [] |[] 通常用于匹配一个字符列表，如：a[mn]z 可匹配 amz 和 anz|
| ** |用于匹配零或多个层级，如 a/**/b 可匹配 "a/b", "a/x/b", "a/x/y/b" 等|
**注意：gitignore只对未被追踪的文件生效**

关于 / 号：
- 当 / 在开头时，表示从项目根目录开始匹配。否则，下级都将匹配。

  举例：/abc 只能匹配 /abc，不能匹配 /x/abc 或 /x/y/abc等。

- 当 / 在末尾时，只匹配目录，否则，则同名的目录和文件都将匹配。

  举例：/abc/ 只能匹配 /abc目录，不能匹配/abc文件。
   
  举例：abc/ 能匹配 /abc/ 或 /x/abc/ 或 /x/y/abc/等，不能匹配 /abc 或 /x/abc 等。

5. **将改动加入暂存区**
```bash
git add .
```
想看当前暂存区的具体情况执行这行
```bash
git status
```
6. **写提交信息**
```bash
git commit -m "内容"
# -m是--message（信息）的缩写
```
7. **修改当前分支名为main**
```bash
git branch -m main
# 这里的-m是--move的缩写（移动/重命名）
```
只看分支用`git branch`即可。若要一次性配置初始分支名为`main`执行这行
```bash
git config --global init.defaultBranch main
```
8. **提交到云端**
```bash
git push -u origin main
# -u即--set-upstream的缩写，意思是设置上游分支（建立追踪关系）
```
其执行逻辑是这样的：
- 把本地的`main`分支推送到云端的`main`
- 假如云端没有`main`分支，git会新建一个远程的`main`分支
- 绑定本地`main`分支与远程`main`分支

假如不加`-u`，那以后的拉取和推送都必须写出远程名和分支名
```bash
git pull origin main
git push origin main
```