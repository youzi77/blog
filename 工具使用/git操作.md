## 切换了远程仓库并更新代码合并
 - 错误： ! [rejected]        master     -> newBranch  (non-fast-forward)   
 - git pull --rebase origin master
##  将远程指定分支 拉取到 本地指定分支上：
git pull origin <远程分支名>:<本地分支名>
## 将远程指定分支 拉取到 本地当前分支上:
git pull origin <远程分支名>
## 将本地当前分支 推送到 远程指定分支上（注意：pull是远程在前本地在后，push相反）：
git push origin <本地分支名>:<远程分支名>
## 将本地当前分支 推送到 与本地当前分支同名的远程分支上（注意：pull是远程在前本地在后，push相反）：
git push origin <本地分支名>

## 本地分支与远程同名分支相关联
git push --set-upstream origin <本地分支名>
// 简写方式
git push -u origin <本地分支名>