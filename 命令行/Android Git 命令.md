### Android Git 命令

- git init
```
// 创建本地仓库
git init
```
- git status
```
// 查看状态
git status
```
- git clone
```
// 获取远程仓库
git clone [url]
// git clone https://github.com/zhaoqingyue/ZQYAndroidNotes.git

// 获取远程仓库development分支代码
git clone -b development https://github.com/zhaoqingyue/ZQYAndroidNotes.git
```
- git remote
```
// 创建远程仓库
git remote add [remote-name] [url]
// git remote add origin https://github.com/zhaoqingyue/ZQYAndroidNotes.git
// origin: 相当于该远程仓库的别名

// 列出所有remote的别名
git remote

// 删除一个 remote
git remote rm [name]

// 重命名 remote
git remote rename [old-name] [new-name]
```
- git add
```
// 添加所有文件
git add . -A     

// 添加指定文件
git add file.txt        
```
- git rm
```
// 从版本库中移除，删除文件
git rm file.txt    

// 从版本库中移除，不删除原始文件
git rm file.txt -cached 

// 从版本库中删除指定文件夹
git rm -r xxx           
```
- git commit
```
// 提交，把缓存内容提交到 HEAD 里
git commit -m "注释"
```
- git push
```
// 把本地提交 push 到远程服务器
git push [remote-name] [loca-branch]:[remote-branch]
// git push origin master:master
// git push -u origin master
```
- git pull
```
// 从远程库中下载新的改动
git pull [remote-name] [branch]
// git pull origin master

pull = fetch + merge

// 从远程库中下载新的改动
git fetch [remote-name]/[branch]

// 合并下载的改动到分支
git merge [remote-name]/[branch]
```
- git branch
```
// 列出分支
git branch

// 创建一个新的分支
git branch (branch-name)

// 删除一个分支
git branch -d (branch-nam)
```
- git checkout
```
// 切换到一个分支
git checkout [branch-name]

// 创建并切换到该分支
git checkout -b [branch-name]
```
- git revert
```
// 撤销最近的一个提交
git revert HEAD

// 取消 commit + add
git reset --mixed

// 取消 commit
git reset --soft

// 取消 commit + add + local working
git reset --hard
```
