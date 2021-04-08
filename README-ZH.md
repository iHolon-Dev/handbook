- # iHolon Git使用规范
- # Git的基础概念
    - ## 本地&远程
        - 本地 工作区与暂存区
            - `git add`
                - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fwatsy0007%2FUq-aQzp3yN.jpeg?alt=media&token=a2506f13-df72-4949-9034-82276fc63c4c)
            - `git commit`
                - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fwatsy0007%2FnSkfLTQyg7.jpeg?alt=media&token=564ac759-0ce9-43a5-baa6-deb1796b15b4)
        - 远程
            - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fwatsy0007%2FEtXoVskux2.svg?alt=media&token=9fac9ec9-6c45-443b-b938-67a175edab9b)
            - 
    - ## 分支
        - > A branch in Git is simply a lightweight movable pointer to one of these commits. The default branch name in Git is master . As you start making commits, you're given a master branch that points to the last commit you made. ... The “master” branch in Git is not a special branch.
**Git 中的分支只是一个轻量级的可移动指针，指向其中的一个提交。Git 中默认的分支名称是 master 。当你开始提交时，你会得到一个指向上次提交的主分支。... 在 Git 中，"master "分支并不是一个特殊的分支。**
        - 查看 Python 的 `git log`
            - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fwatsy0007%2FMP07nSE1gz.png?alt=media&token=7dff0ec1-e5e4-4d93-be08-556f4061163f)
        - 查看我们的分支
            - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fwatsy0007%2Fy8KCjr7SdV.png?alt=media&token=27b6beda-d045-4d06-a671-27d67ef52a5a)
            - 
    - ## 标签
        - > Tags are ref's that point to specific points in Git history. Tagging is generally used to capture a point in history that is used for a marked version release (i.e. v1. 0.1). A tag is like a branch that doesn't change. Unlike branches, tags, after being created, have no further history of commits.
**标签是指向Git历史上特定点的ref，一般用于捕捉历史上某个被标记的版本发布（如v1. 标签通常用于捕捉历史上的一个点，并用于标记版本发布（如v1.0.1）。一个标签就像一个分支，不会改变。与分支不同的是，标签在创建之后，就没有进一步的提交历史了。**
        - 使用场景: 一般用于发版
- # 协作
    - ## 分支管理策略
        - 新建分支 `git checkout -b dev`
        - 合并到当前分支 `git merge dev`
    - ## 处理冲突
        - `git merge feat1`
        ```shell 
        Auto-merging readme.txt
        CONFLICT (content): Merge conflict **in** readme.txt
        Automatic merge failed; fix conflicts **and** **then** commit the result.
        ```        
        - 查看异常`readme.txt`
        ```shell
        Git tracks changes of files.
        <<<<<<< HEAD
        Creating a new branch is quick & simple.
        =======
        Creating a new branch is quick AND simple.
        >>>>>>> feature1
        ```
        - Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容

    - ## merge 与 rebase
        - ![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2Fwatsy0007%2FBPDWpBHNuG.png?alt=media&token=370cec7c-0fff-49ca-baf1-19cccb867a1f)
        - 
- # 规范
    - ## 分支规范
        - git分支很灵活, 衍生很多种规范, 基于我们当前的规模, 选择最方便的可实践规范
        - **分支**
            - `master` 主干分支, 用于发版
            - `deveop` 开发分支, 可以用于测试环境
            - `feature` 功能开发分支
                - `feature/register_notify_to_dingding`
            - `fix` 修复bug分支 (线上紧急异常)
                - `fix/register_500`
        - 注意
            - 1. 针对功能进行分支, 避免1个分支多功能, 或者1个功能多个分支
            - 2. rebase > merge
                - [git rebase 还是merge的使用场景最通俗解释](https://www.jianshu.com/p/4079284dd970)
                - 简化规则
                    - 及时rebase 上游分支 `git rebase develop`
                    - 本地当前分支多次提交可以 rebase 合并 commit message
                    ```shell
                    (dev)> git checkout -b feature/test
                    # 功能开发 1
                    (feature/test) > git commit -a -m '1'
                    # 功能开发 2
                    (feature/test) > git commit -a -m '2'
                    # 功能开发3
                    (feature/test) > git commit -a -m '3'
                    # 准备提交, rebase上游
                    (feature/test) > git rebase -i dev
                    pick 0191926 1
                    pick f573278 2
                    pick 83c1d1b 3
                    修改为
                    pick 0191926 1
                    squash f573278 2
                    squash 83c1d1b 3
                    保存之后, 修改commit message
                    ```

    - ## commit log 规范
        - 开头一行 `feat|fix|refactor`简介, ~~空格, 起多行详细说明~~
        - `feat: 新注册用户钉钉通知`
        - `fix: 后台图表详情页打不开`
        - `refactor: 优化信息流首页的 api 响应速度`
