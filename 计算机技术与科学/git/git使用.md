**stash 命令官方解释**：当你想记录共工作目录和索引的当前状态，但又想返回一个干净的工作目录时，请使用stash该命令将保存至本地修改，并恢复工作目录以匹配头部提交。
**stash 相关命令**
git stash 保存当前未commit的代码
git stash save "comment"   保存未commit的代码并添加备注
git  stash list 列出stash的所有记录 
git  stash clear 删除stash的所有记录
git stash apply 应用最近一次的stash
git stash pop 应用最近一次的stash ，随后删除记录
git stash drop 删除最近一次的stash
