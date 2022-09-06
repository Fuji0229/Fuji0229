git flow ：约定的流程使用git分支
![[Pasted image 20220906101539.png]]
常用分支：
- 生产分支（master）：
  只能从其他分支进行合并，不能在该分支进行修改。
- 补丁分支（hotfix）：
  在生产环境发现新的bug时，基于master分支创建一个Hotfix分支，然后再Hotfix分支上进行修复，完成Hotfix后要把Hotfix分支合并回master和develop分支。
- 发布分支（release）：
  需要发布新功能的时候，从Develop分支创建一个release,在Release分支进行测试并修复bug，完成Release后把Release分支合并至master分支和Develop分支
- 开发分支（Develop）：
  主开发分支，包含所有要发布到一个Release的代码，主要用于其他分支的合并
- 功能分支（feature）
  用来开发新功能，开发完成后，合并到Develop分支进行下一个Release的发布