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
Git flow的具体使用细节：
- 新建Git仓库时，会默认创建一个主分支master，master分支用于生产环境，必须保证master代码上的稳定性，不能直接在master分支上进行修改提交。因此，需要基于master分支创建一个开发分支（Develop），用于保存开发好的相对稳定的功能，master和Develop分支用是仓库的常驻分支，一直会保留在仓库中。
	![[Pasted image 20220906103119.png]]
- 新的开发任务来了，为了保证Develop分支的稳定性，就需要从Develop分支新创建功能分支（feature）进行开发，等到开发完毕后再把feature分支合并到Develop分支
	![[Pasted image 20220906104553.png]]
- 新功能合并到Develop分支之后，想要把新功能发布到开发环境，那就需要从开发分支创建Release分支，在Release分支测试完成之后，把Release分支合并到master分支和Feature分支。
  ![[Pasted image 20220906105626.png]]
- Release分支合并到master分支之后，在master分支上打标签用于发布：
  ![[Pasted image 20220906110331.png]]
- 代码发布到生产环境后，用户在使用过程中给我们反馈了bug问题，这时候需要从master分支创建hotfix分支，用于修复bug，bug修复好之后，把hotfix分支分别合并到master分支和Develop分支上。
  ![[Pasted image 20220906110532.png]]
Git flow工具--更标准化的执行git flow
- 安装：
	- macOS：
	  $ brew install git-flow‌
	- Linux：
		$ apt-get install git-flow
	- Windows:

$ wget -q -O - --no-check-certificate https://github.com/nvie/gitflow/raw/develop/contrib/gitflow-installer.sh | bash
‌
**使用**‌

-   **初始化:** git flow init
-   **开始新Feature:** git flow feature start MYFEATURE
-   **Publish一个Feature(也就是push到远程):** git flow feature publish MYFEATURE
-   **获取Publish的Feature:** git flow feature pull origin MYFEATURE
-   **完成一个Feature:** git flow feature finish MYFEATURE
-   **开始一个Release:** git flow release start RELEASE [BASE]
-   **Publish一个Release:** git flow release publish RELEASE
-   **发布Release:** git flow release finish RELEASE 别忘了git push --tags
-   **开始一个Hotfix:** git flow hotfix start VERSION [BASENAME]
-   **发布一个Hotfix:** git flow hotfix finish VERSION

	  