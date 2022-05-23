# 一、 问题描述

如果你在`七夕`（没错就是`2021年8月14日`）的这一天`刚好加班`，`又刚好去访问了全球最大的同性交友网站`，`又刚好去更新提交代码`，`又或你创建了一个新的仓库送给自己`，`又刚好想把这个仓库送给（push）github`，你就刚好会遇到这个问题：`remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.`

具体如下：

```git
(yolov4) shl@zhihui-mint:~/shl_res/5_new_project/Yolov4_DeepSocial$ git push origin master
Username for 'https://github.com': shliang0603
Password for 'https://shliang0603@github.com': 
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: unable to access 'https://github.com/shliang0603/Yolov4_DeepSocial.git/': The requested URL returned error: 403
(yolov4) shl@zhihui-mint:~/shl_res/5_new_project/Yolov4_DeepSocial$ 
```

纳尼？老夫就是许久没有建仓，这是什么情况，大概意思就是`你原先的密码凭证从2021年8月13日`开始就不能用了，`必须使用个人访问令牌（personal access token）`，就是把你的`密码`替换成`token`！

# 二、 [github](https://so.csdn.net/so/search?q=github&spm=1001.2101.3001.7020)为什么要把密码换成token

[github官方解释](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)

1、修改为token的`动机`

我们描述了我们的`动机`，因为我们宣布了对 API 身份验证的类似更改，如下所示：

近年来，GitHub 客户受益于 GitHub.com 的许多`安全增强功能`，例如双因素身份验证、登录警报、经过验证的设备、防止使用泄露密码和 WebAuthn 支持。 这些功能使攻击者更难获取在多个网站上重复使用的密码并使用它来尝试访问您的 GitHub 帐户。 尽管有这些改进，但由于历史原因，`未启用双因素身份验证`的客户仍能够仅使用其`GitHub 用户名和密码`继续对 Git 和 API 操作进行身份验证。

从 `2021 年 8 月 13 日`开始，我们将在对 Git 操作进行身份验证时`不再接受帐户密码`，并将要求使用`基于令牌（token）的身份验证`，例如个人访问令牌（针对开发人员）或 OAuth 或 GitHub 应用程序安装令牌（针对集成商） GitHub.com 上所有经过身份验证的 Git 操作。 您也可以继续在您喜欢的地方使用 SSH 密钥（[如果你要使用ssh密钥可以参考](https://cloud.tencent.com/developer/article/1861466)）。

2、修改为token的好处

`令牌`（`token`）与基于密码的身份验证相比，令牌提供了许多`安全优势`：

-   `唯一`： 令牌特定于 GitHub，可以`按使用`或`按设备`生成
    
-   `可撤销`：可以随时单独撤销令牌，而无需更新未受影响的凭据
    
-   `有限` ： 令牌可以`缩小范围`以仅允许用例所需的访问
    
-   `随机`：令牌不需要记住或定期输入的更简单密码可能会受到的字典类型或蛮力尝试的影响
    

# 三、 如何生成自己的token

1、在`个人设置页面`，找到`Setting`（[参考](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)）  
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9d95877fed94f919d619bc3d7d1f93d.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

2、选择开发者设置`Developer setting`

![在这里插入图片描述](https://img-blog.csdnimg.cn/2df5f13096954c4f96013aee40cfa35b.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

3、选择个人访问令牌`Personal access tokens`，然后选中生成令牌`Generate new token`

![在这里插入图片描述](https://img-blog.csdnimg.cn/d7cc16a642c2438686285b392b872e10.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

4、设置token的有效期，访问权限等

选择要授予此`令牌token`的`范围`或`权限`。

-   要使用`token`从命令行访问仓库，请选择`repo`。
-   要使用`token`从命令行删除仓库，请选择`delete_repo`
-   其他根据需要进行勾选

![在这里插入图片描述](https://img-blog.csdnimg.cn/5468bf3217644370be38396d6bcee2f2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

5、生成令牌`Generate token`

![在这里插入图片描述](https://img-blog.csdnimg.cn/c887371c467d439ea24e5b50f317e7d3.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

如下是生成的`token`![在这里插入图片描述](https://img-blog.csdnimg.cn/cc0bf9cb238648d6b91675aef4650120.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

`注意：`

记得把你的token保存下来，因为你再次刷新网页的时候，你已经没有办法看到它了，所以我还没有彻底搞清楚这个`token`的使用，后续还会继续探索！  
![在这里插入图片描述](https://img-blog.csdnimg.cn/571ad474bd3e4714b9b50e68c4406c46.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

6、之后用自己生成的`token`登录，把上面生成的`token`粘贴到`输入密码的位置`，然后成功push代码！

![在这里插入图片描述](https://img-blog.csdnimg.cn/5505a44f099b49568d25e3b254fbafca.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTAxMDE5OA==,size_16,color_FFFFFF,t_70)

也可以 把`token`直接添加远程仓库链接中，这样就可以避免同一个仓库每次提交代码都要输入`token`了：

> `git remote set-url origin https://<your_token>@github.com/<USERNAME>/<REPO>.git`

-   `<your_token>`：换成你自己得到的`token`
-   `<USERNAME>`：是你自己`github`的`用户名`
-   `<REPO>`：是你的`仓库名称`

例如：

> `git remote set-url origin https://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@github.com/shliang0603/Yolov4_DeepSocial.git/`

# 四、 常见问题

`有些问题我也没有遇到过，大家共同探讨吧，我会在评论区把大家遇到的问题做个汇总！`

1、如果 push 等操作没有出现`输入密码选项`，请先输入如下命令，之后就可以看到输入密码选项了

> `git config --system --unset credential.helper`