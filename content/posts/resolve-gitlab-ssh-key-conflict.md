+++
title = '解决gitlab ssh公钥冲突问题'
date = 2024-10-29T17:14:00+08:00
draft = false
+++

在使用gitlab时遇到这样一个问题，对于同一个密钥，一个用户将其添加到个人设置里的SSH密钥里后，另一个用户就不能再添加相同的密钥了，会提示密钥指纹重复，也就是说，密钥在gitlab里是全局唯一的，这个规则对于项目的部署密钥也适用。

因此，在gitlab中，每个密钥就唯一的对应了一个用户，当然一个用户可以设置多个密钥。这就导致了一个问题：

假设有一台服务器HostA，它的默认密钥(id_rsa)是SA，用户UA将SA添加到他的SSH密钥集里，用户UA有一个项目PA，于是用户就可以在这台服务器上拉取代码了。这时来了一个新的用户UB，他也想在这台服务器上拉取他的项目PB的代码，假如他直接使用地址`ssh://git@gitlab.com/PB`来拉取代码，会提示没有权限，因为这时登录使用的是默认的SSH密钥id_rsa，而这个密钥已经被用户UA添加到他的密钥集里了，gitlab会认为当前拉取PB项目的是用户A，而用户A并不是项目PB的成员，因此gitlab判断当前用户没有权限而拒绝处理。

要解决这个问题，用户B可以在服务器上生成一对新的密钥，然后为gitlab.com配置一个别名，在这个别名中使用新的密钥进行SSH登录，就可以解决公钥冲突的问题，具体步骤如下：

1. 生成新的密钥对：
```shell
ssh-keygen -t rsa
```
根据提示生成新的密钥文件，注意在输入密钥文件保存位置时，不要使用默认值，需要输入一个新的路径以防覆盖默认的密钥文件，如`~/.ssh/user_b_rsa`

2. 将公钥文件内容添加到gitlab的密钥集中
以上面生成的密钥为例，将`~/.ssh/user_b_rsa.pub`文件的内容添加到gitlab中。

3. 修改`~/.ssh/config`文件，增加gitlab.com域名的别名配置
示例：
```
Host gitlab-alias.com # 别名
  HostName gitlab.com # 真实域名
  Port 22
  IdentityFile ~/.ssh/user_b_rsa # 注意这里要使用新的密钥
```

4. 使用新的别名克隆项目
例如：ssh://git@gitlab-alias.com/project.git