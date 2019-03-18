# MutipleAcountForGit

在 Windows 下配置多个 git 账号的教程
本文主要的目的是记录下 window 系统下配置多个 git 账号的过程，一是可以加深记忆，二是提供给更多需要的人（当然也参考了前辈的教程）。

### 1.生成并部署 SSH key

安装好 Git 客户端之后，打开 git bash,输入以下命令生成 user1 的 ssh key

<pre>ssh-keygen -t rsa -C "user1@email.com"；</pre>

在当前用户的.ssh 目录下会生成 id_rsa 私钥文件和 id_rsa.pub 公钥文件，将 id_rsa 文件中的内容复制并添加到 github 账户（setting->profile->ssh key）。然后在 git bash 中输入一下命令测试该用户的 SSH 密钥是否生效。

<pre>ssh -T git@github.com</pre>

若连接成功则提示 <pre>Hi user1! You've successfully authenticated, but GitHub does not provide shell access.</pre>
注意:该命令仅限于文件名为 id_rsa 的密钥。

接着生成 user2 的密钥，此处不能再使用 id_rsa 的文件名，否则会覆盖之前的密钥文件。

<pre>ssh-keygen -t rsa -f ~/.ssh/id_rsa2 -C "user2@email.com"</pre>

再将 user2 的公钥文件添加至 github 中。

测试 user2 的 ssh 连接时需要指定密钥文件：

<pre>ssh -T git@github.com -i ~/.ssh/id_rsa2</pre>

也可以使用 ssh agent 添加密钥后进行测试。因为系统默认只读取 id_rsa，为了让 ssh 识别新的私钥，可以使用 ssh-agent 手动添加私钥：

<pre> 
ssh-agent bash
ssh-add ~/.ssh/id_rsa2
</pre>

### 2.配置 config 文件

在.ssh 目录下创建一个 config 文本文件，每个账号配置一个 Host 节点。主要配置项说明：

<pre>
Host 　　主机别名
HostName 　　服务器真实地址
IdentityFile 　　私钥文件路径
PreferredAuthentications 　　认证方式
User 　　用户名
</pre>

Host 的名字可以取为自己喜欢的名字，不过这个会影响 git 相关命令，例如：
Host mygithub 这样定义的话，命令如下，即 git@后面紧跟的名字改为 mygithub

<pre>git clone git@mygithub:PopFisher/AndroidRotateAnim.git</pre>

配置文件如下：

<pre>
######## #yindi.zeng
Host yindi.zeng
HostName git.tools.pjbest.com
IdentityFile D:\\Users\\yindi.zeng\\.ssh\\id_rsa
PreferredAuthentications publickey
User yindi.zeng
</pre>

<pre>
######## #veriazzzz
Host veriazzzz
HostName github.com
IdentityFile D:\\Users\\yindi.zeng\\.ssh\\id_rsa2
PreferredAuthentications publickey
User veriazzzz
</pre>

再通过终端测试 SSH Key 是否生效

<pre>
ssh -T git@veriazzzz
ssh -T git@yindi.zeng
</pre>

### 3.配置用户和邮箱

如果之前配置过全局的用户名和邮箱，需要取消相关配置，再在各仓库下配置相应的用户名和邮箱。

<pre>
git config --global --unset user.name
git config --global --unset user.email
</pre>

为各仓库单独配置用户名和邮箱

<pre>
git config veriazzzz.name "user1"
git config veriazzzz.email "user1@email.com"
</pre>

如果原先使用 HTTPS 通信，则需要修改远程仓库地址(自己的仓库可以用 ssh 和 https，如果是拉别人仓库，需要改成 https)

<pre>
git remote rm origin
git remote add origin git@veriazzzz:veriazzzz/xxxxx.git
</pre>
