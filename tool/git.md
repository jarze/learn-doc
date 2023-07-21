
> git 常用命令

# config

```bash

git config -l

git config --local user.name xxx
git config --local user.email xx@xx

git config --global https.proxy http://127.0.0.1:1086
git config --global http.proxy http://127.0.0.1:1086

git config --global --unset http.proxy
git config --global --unset https.proxy

```

# submodule

```bash

git submodule update --init --recursive
git submodule foreach --recursive

git submodule update --init --remote
git submodule foreach yarn
git submodule foreach 'yarn build'

```

# other

```bash

git remote -v

git pull --rebase origin dev

git rebase -i HEAD~3

```


```bash

git show

# 修改提交信息
git commit --amend -m 'xxxxxxx'
git commit --amend --only -m 'xxxxxxx'

git commit --amend --author "name <email>"
```