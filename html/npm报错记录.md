#### npm 提示128错误，提示无法从git@github.com下载

- 解决办法：生成秘钥，添加到github中
  - `ssh-keygen -t rsa -C"you_email"`
  - `ssh -T id_dsa.pub`
  - 添加到github中的秘钥中

