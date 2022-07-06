# GIT常用命令
### 合并单个文件
1. 提交单个文件
2. 合并单个文件的commit

```shell
git checkout <branch> <file>
```

## 分支管理
```shel
# Checkout远程分支
git checkout -b localBranch origin/remoteBranch
```

## 代码回滚
```shell
# 回滚本地版本
git reset --hard <commit>
# 强制推送到远程仓库
git push -f
```

git reflog show <branch name>



## 合并commit
```sh
git cherry-pick <commit>
```

## 设置代理
```shell
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080

git config --global --unset http.proxy
git config --global --unset https.proxy
```

## Tag管理
```shell
# 删除远程tag
git push origin :refs/tags/<name>
```

### 关联远程分支

```shell
git branch -u <origin/new_branch>
```

