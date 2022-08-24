# Git的一些不常见但有用的操作

### 推送现有文件夹

```sh
cd existing_folder
git init
git remote add origin <git-url>
git add .
git commit -m "Initial commit"
git push -u origin master
```

### 子模块

```sh
git submodule add git@gitxxxxxxx.git
```

### 更名子模块并同步

```sh
git mv 子模块旧名字 新名字
git submodule sync
git add -u
git commit
git push 
```

