# brew使用

## 切换镜像源

```
 替换brew.git:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
 
# 替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
 
# 替换homebrew-cask.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
 
# 应用生效
brew update
# 替换homebrew-bottles:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
# 应用生效2
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
# 查询当前使用的shell
echo $SHELL
```

## 还原镜像源

```
# 替换brew.git:
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
 
# 替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git
 
# 替换homebrew-cask.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://github.com/Homebrew/homebrew-cask.git
 
# 应用生效
brew update
 
# 删除.bash_profile变量
vim  ~/.bash_profile
# 删除如下变量
# export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
 
# 执行更新
source ~/.bash_profile

```

## 切换为阿里

```
cd "$(brew --repo)"
  
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
  
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
  
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
  
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile

```

## 安装nginx

```
brew install nginx  安装
brew info nginx 查看详情
nginx -v nginx版本
brew services start nginx  启动nginx
brew services stop nginx  关闭nginx
```
