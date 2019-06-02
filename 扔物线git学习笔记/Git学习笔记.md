版本控制系统
	在文件的修改历程中保留修改历史，让你方便地撤销之前对文件的修改操作
	vcs：中央式版本控制系统
	DVCS:分布式版本控制系统

DVCS
	分布式版本控制系统：除了有中央仓库之外，还有本地仓库；团队成员每个人都有一个本地仓库，这个仓库包含了所有的版本历史，简单说明，每个人都可以在自己的机器上提交代码、查看历史，无需与中央仓库交互-取而代之，你只需要与本地仓库交互即可

	中央式版本控制系统的中央仓库主要功能：保存版本历史、同步团队代码。分布式版本控制系统则将保存版本历史的工作交给了每个团队的本地仓库中，中央仓库只剩下同步这一个主要任务，中央仓库也保存了历史版本，但是历史版本更多的是作为团队间的同步中转站

# 新公司使用Git管理代码，怎么才能快速上手 #
	清除命令行 reset 完全清理 clear 只是让命令行向下翻页

	示例(将Android开发艺术的知识点放入git仓库)：
		1、github创建仓库，使用git clone xxx将仓库clone到本地
			git clone https://github.com/xxx xxxx(以新名称将仓库clone到本地)
		2、在本目录下git log 
			commit 01c7822b96c26bf66b9abe56b1106c85127e3eed  //SHA1
			Author: wuaoke <987603594@qq.com>
			Date:   Mon May 20 14:30:36 2019 +0800
			
			    Initial commit
			注：git命令不往下走的时候 可以按q键继续

		3、文件夹中放入一个文件，并提交，注意提交之前使用git status来查看工作目录当前的状态
			$ git status
			On branch master//你在master分支(branch)
			Your branch is up-to-date with 'origin/master'. //你的branch分支没有落后于origin/master分支
			Untracked files:// Untracked 未被追踪的文件 
			  (use "git add <file>..." to include in what will be committed)//你可以使用git add追踪文件
			
			        "Android\345\274\200\345\217\221\350\211\272\346\234\257\345\255\246\344\271\240\350\265\204\346\226\231/"
			
			nothing added to commit but untracked files present (use "git add" to track)

		4、使用git add  文件名 追踪文件  (git add .应该是追踪所有未被追踪的文件) 可以看到文件从Untracked(为被追踪)变成staged（已暂存）,意思是这个文件中被改动的部分被记录进了staging area(暂存区  汇集待提交改动的地方)

		5、使用git commit 提交  会进入一个填写提交信息的界面  ，信息输入完成后，不用点击回车，点击Esc键，然后连续两次点击大写Z，则提交成功！  git commit -m '你提交的信息'
		6、输入git log
			$ git log
			commit 901e6e0f77367744e6027318a69420a83194fc34//我们最新提交的会出现在最上面
			Author: wuaoke <987603594@qq.com>
			Date:   Mon May 20 14:48:19 2019 +0800
			
			    第一次提交
			
			commit 01c7822b96c26bf66b9abe56b1106c85127e3eed
			Author: wuaoke <987603594@qq.com>
			Date:   Mon May 20 14:30:36 2019 +0800
			
			    Initial commit

		7、将代码提交到中央仓库 git push 提交代码到中央仓库

	注意：在向中央仓库提交代码时，会让你频繁输入账户和密码，解决方案：
	 此处是使用https克隆仓库
		1、$ git config --global credential.helper wincred   （wincred 避开、畏惧）
		2、$ git config --global credential.helper store  Git把你的密码以明文形式保存在你的电脑上  (store 储存)

	 使用SSH克隆仓库 则使用ssh秘钥而不再使用用户名和密码 
		使用SSH连接到github
			1、查看本地是否有SSH秘钥
				ls -al ~/.ssh  如果有会展示出来
				如果没有则去创建SSH秘钥
					ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
			2、将SSH秘钥添加到SSH-agent ....比较麻烦，以后再说吧

			3、测试SSH连接 ssh -T git@github.com


# 团队工作的基本工作模型 #
	团队开发时，所有的一切都是并行进行的，你提交别人也提交，你push别人也push

	同时将代码提交到中央仓库，你怎么取下来？
		$ cd ../AndroidArt  退出到上个目录并进入AndroidArt目录
		git pull 从中央仓库拉取文件并合并 

	当中央仓库含有你没有的commit记录的时候，你push就会失败，怎么解决？
		push发生冲突，如果远端仓库含有本地仓库没有的commit，则会拒绝本次push。解决方式是先pull，如果我们已经git commit使用git pull会自动进行git commit，这是因为不仅我们向远程仓库提交会进行检查，当远程仓库被pull时，如果检查到本地的commit与它的不同，就会把远端和本地独有的commit合并并生成一个新的commit，而且会自动填写commit的信息(可以自行更改)
		
		这种把不同内容进行合并，生成新的提交的操作，对应的git指令是merge，事实上git pull指令内部实现就是把远程仓库使用git fetch取下来之后在进行merge操作的
		
		当git pull有冲突时，修改完冲突后 使用git commit -m 'xxx'报错  这时候使用git commit -a 解决问题后并合并，然后提交

# HEAD、master、与branch #
	HEAD：
		当前commit的引用  ，commit在哪里，HEAD就在那里，这是一个永远指向当前commit的引用，永远可以用HEAD来操作commit
		HEAD是git中一个独特的引用，它是唯一的

	branch：
		HEAD除了可以指向一个commit，还可以指向一个branch，当它执行某个branch的时候，会通过这个branch间接地指向某个commit；当HEAD在提交时自动向前移动时，会像一个拖钩一样带着他所指向的branch一起移动
	
	master：
		默认branch，git默认分支(主分支)、

	branch的创建、切换、删除
	
	git branch xx 创建分支
	git checkout xx 切换分支
	git checkout -b 名称  上面两步的集合体
	git branch -d xx  删除branch

	git branch 查看当前所有分支，并查看当前所在的分支
	
	1、分支切换到那个HEAD就会指向那个分支，当前HEAD指向的分支并不能删除 
	2、由于branch在git中只是一个引用，所以删除分支只会删除这个引用，并不会删除这个commit，如果一个commit并不在任何一条branch上，会给git的回收机制回收
	3、由于安全机制，没有合并到master的branch会删除失败，如果非要删除，使用git branch -D xx删除

# push的本质 #
	实质上，push做的事就是将当前branch的位置(即它指向那个commit)上传到远端仓库，并把它路径上的commits一起上传
	git checkout xxx切换分支
	git push origin xxx  我要push到origin这个仓库的xxx分支
	
	git 2.0以及之后版本，git push只能上传那些之前从远端clone或者pull下来的分支，若需要push本地的分支，需要手动指定目标仓库和目标分支

	push只是上传当前branch的指向，，并不会把本地的HEAD指向一起上传到远程仓库，远程仓库的HEAD永远指向它的默认分支

合并两个独立启动仓库的历史
	git pull origin master --allow-unrelated-histories

# merge：合并commits #
	pull的内部操作实际就是把远程仓库取到本地后(使用的是fetch)，再用一次merge来把远端仓库的commits合并到本地。
	
merge含义和用法
	指定一个commit，把它合并到当前commit。
	实际：
		从目标commit和当前commit(即HEA指向的commit)分叉的位置起，把目标commit的路径上的所有commit的内容一并应用到当前commit，然后自动生成一个新的commit。(看图)

	作用：1、合并分支  当一个分支的开发已经完成，需要把内容合并回去的时候，用merge来合并
		 2、pull的内部操作 先把远程仓库的渠道本地后，再一次merge把远程仓库的commits合并到本地

	特殊情况：
		A、冲突
			merge做合并的时候，有一定的自动合并能力的
				1、不同分支改不同的文件，比如两个分支，一个改A文件，一个改B文件，那么合并后就是即改了A文件，又改了B文件，动作会自动完成
				2、不同branch改一个文件的不同位置，一个分支改文件的第一行，一个改文件的第二行，merge也是可以自动合并的
				3、不同分支改变同一个文件的同一个位置，就会发生合并冲突(Conflict)
					解决冲突后，手动commit一下
					放弃解决冲突，取消merge  git merge --abort  仓库会回到merge之前的状态

		B、HEAD领先于目标commit
			如果merge时的目标commit和HEAD处的commit并不存在交叉，而是HEAD领先于目标commit(通俗解释，只是一个分支，目标commit在HEAD处的前面，比如画图中 2和3commit 2相当于目标commit，3相当于HEAD处的commit，那么merge就没必要再创建一个新的commit了，因为并没有什么需要合并的。)

		C、HEAD落后于目标commit
			git会把HEAD(以及它指向的branch，如果有的话)移动到目标commit  术语fast-forward(快速前移)

# Feature Branching：最流行的工作流 #
	总结(此工作流的工作模型)：
		1、任何新的功能(Feature)和bug修复全都新建一个branch来写
		2、branch写完后，合并到master，然后删掉这个branch

	可以解决代码共享和一人多任务		
	场景1(代码共享)：
		你新建一个分支开发某个功能
		git checkout -b test

		开发完之后将次分push到远程仓库
		git push origin test

		然后找人给你review(审阅)，别人操作
		git push
		git checkout test

		此时别人说功能完成可以merge到master分支，此时你在本地
		git checkout master
		git pull   merge之前最好pull一下，让master更新到与远程仓库同步
		git merge test  # 将tast分支合并到master分支

		然后将合并后的结果push到远程仓库
		git push 
		git branch -d test
		git push origin -d test #使用d参数吧远程仓库的branch也删除了
		
		git push origin -d xxx删除远程仓库的branch

	场景2(一人多任务):
		在独立的branch做事时，同事来找你做新功能，此时你可以吧目前branch的代码简单收尾并做个提交，然后回到master去创建一个新的branch来做新工作，做好后只需要使用checkout切换到原来的那个branch就好

# 关于add #
1、add后面加个".":全部暂存
	git add .  直接把工作目录下的所有改动全部放进暂存区  (midified 修改的)

# 看看我都改了什么 #
	git log查看历史记录  只是大概的commit的信息
	git log -p  查看详细历史
	git log --stat查看简要统计
	git reflog 查看你的每次命令，查看HEAD的移动历史

	查看当前commit具体信息
	git show
	
	查看任意一个commit
	git show sha1值

	查看任意一个commit内部的文件
	git show sha1 --文件名

	查看未提交的内容
	diff
	
	比对暂存区与上一条提交
	git diff --staged  通俗讲就是让你知道 你立即git commit之后，你提交的内容是什么  还有个 git diff --cache

	比对暂存区与工作目录
	git diff 可以显示工作目录与暂存区之间的不同  通俗讲就是，现在你把所有文件都add，你会向向暂存区增加那些内容

	比对工作目录与上一条提交
	git diff HEAD  通俗讲就是，知道git add和git commit所提交的内容 如果没有add，则无法追踪

	插入知识：
		版本库，工作区，暂存区(staged)
		工作区类似 AndroidArt 这个代表本地仓库的目录就是工作区
		版本库包含暂存区、HEAD指针、默认创建的master分支  .git文件夹就是版本库，当你git add时就是将数据放到暂存区,而git commit就是将暂存区的内容全部提交到当前分支

# 不喜欢merge的分叉，用rebase吧 #
	merge之后commit历史会出现分叉，这些分叉再会和的结构会让人觉得混乱而且难以管理，如果不希望commit历史出现分叉，可以用rebase来代替merge

rebase
	在新位置重新提交

	git checkout dev 
	git rebase master  dev的提交历史会直接放到master中重新组成一条线

	rebase之后
	git checkout master
	git merge dev  把HEAD移到最新的commit

刚刚提交的代码，发现写错了怎么办？
	在本地修改再提交是一种方法，还可以这样用
	本地修改后 git add <File>
	git commit --amend(修正)  将工作区与暂存区的里的内容合并后创建一个新的commit，用这个新的commit将老的commit替换掉

写错的不是最新的提交，而是倒数第二个
	git commit --amend 修复的是最新的commit的错误，但是如果是倒数第二个commit错误了怎么办

	rebase -i(interactive简写) 交互式rebase
		不是最新的提交有问题就无法使用git commit --amend来修复，要用rebase

		当发现倒数第二个错了以后 ：
			git rebase -i HEAD^^ (往前移的commit数量是^^的数量)
			git rebase -i HEAD~数字 (往前移的commit数量是~后边跟的数字的大小)
		紧接着进入编辑界面：选择commit和对应的操作
			列出将要被rebase的所有commits，排序是正序，旧的commit会被排在上面，新的排在下面
		想要修复那个commit，就将pick改为edit，然后退出编辑界面，然后再去修改commit
			git add <File>
			git commit --amend(修正)  
		继续rebase过程
			git rebase --continue

丢弃刚才的提交
	git reset --hard HEAD^跟上边一样  还有个git reset --hard HEAD~1...  撤销的是版本库的数据

想丢弃的并不是最新的提交
	场景：一个commit，你刚写完的时候并不觉得有什么问题，几个提交之后，突然发现有问题需要丢弃刚才的提交
	使用交互式rebase撤销提交
		除了修改一些旧的commits，还能用于撤销提交
		命令如下：
			git rebase -i HEAED^^
			然后使用方向键选择要删除的行 ，使用delete删除  双击大写z完成

代码已经push上去发现写错了
	1、出错的代码在你的branch上
		你在push之后发现错误commit，你可以使用rebase -i直接修改出错的commit，但是当你push时会报错，因为中央仓库含有你没有的commits。这次并不是因为同事的原因导致冲突，而是因为你自己修改了commit导致的，所以这时候就希望本地能够强制覆盖远程仓库，使用命令：
			git push origin branch -f(force的缩写)  忽略冲突，强制push(这个操作的先决条件是你能够掌控所有的未知情况)
	
	2、出错的内容已经合并到master
		增加一个新的提交，把之前提交的内容给抹掉。
			git revert HEAD^ 增加一个新的commit与原来的commit的操作完全相反(我的理解，就是撤销上一个commit的操作，然后自动生成一个新操作)，然后再push上去，这种操作只是将原来的commit的行为给翻转了，而且还在操作历史中

reset的本质--不只可以撤销提交
	最新的commit写错时，可以使用reset --hard 来吧这个commit给撤销，其实本质并不是撤销，而是将HEAD指针带着branch移动到当前commit的父commit上，从而起到撤销的效果

	so，reset --hard不仅可以撤销提交，还可以把HEAD和branch移动到任何地方。
		git reset --hard branch
	
	reset命令可以重置HEAD和branch的位置，不过在重置他们的同时，对工作目录可以选择不同的操作，而对工作目录操作的不同，就是通过reset后面跟的参数来确定的

	reset --hard 重置工作目录  git reset --hard HEAD^不仅会撤销commit，还会将工作区的新改动全部擦除，不管是否已经放置到暂存区
	reset --soft 保留工作目录和暂存区的内容，并把重置HEAD带来的新的差异放进暂存区
	reset 不加参数：保留工作目录，并清空暂存区

checkout的本质
	git checkout branch名  实质就是将HEAD指向指定的branch，然后签出这个branch所对应的commit的工作目录
	
	git checkout --<file>  撤销工作区的内容  另一层意思是使用版本库的内容替换工作区的版本
	git reset HEAD <File> 清空暂存区的内容 
	git rm <File> 将版本库的文件删除 并git commit 就可以删除版本库中的文件了

	checkout与reset不同的地方在于  reset是HEAD和branch一起移动，而checkout只是将HEAD移动到其它地方，这时候HEAD并不会带着branch一起移动
	git checkout --detach 将HEAD与branch脱离

紧急情况
	场景：你正在一个branch开发，紧急情况需要处理  
		git stash 临时存放工作目录的改动 把工作区暂时隐藏  
		然后切换master分支，处理问题之后切换回刚才隐藏工作区的分支，使用：
		git stash pop 你之前存储的就又回来了  这个恢复的同时将stash内容也删除了

		git stash list 查看stash列表
		git stash apply ... 回复stash 但不删除stash的内容 使用git stash drop删除

branch删除了才想起来还有用		
	使用git reflog 可以查看HEAD的移动历史 找到删除branch的那条操作的commitid  然后使用git checkout  commitid 删除的branch就回来了

	git reflog branch 查看指定branch的HEAD移动历史

		
	


	


	
	
		

			
	
						
	




		


