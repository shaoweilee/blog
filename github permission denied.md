# github permission denied 问题解决

本机生成了sshkey以后，也添加到github的设置里了，但是push的时候还是出现permission denied问题，解决方法是对本地id_rsa密钥进行ssh-add操作：

```
ssh-add ~/.ssh/github_id_rsa
```





实际上，添加一个sshkey的步骤是这样的：

1. 生成sshkey：`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
2. 把sshkey**私钥**添加到本机ssh客户端：`ssh-add ~/.ssh/id_rsa`
3. 把sshkey公钥添加到github上。

