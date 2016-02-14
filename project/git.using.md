## Git 使用案例

### Document

1. [GitSubmoduleTutorial](https://git.wiki.kernel.org/index.php/GitSubmoduleTutorial#Removal)
2. [Git.github.document](https://github.com/git/git/tree/v1.8.3-rc0/Documentation)

### submodule

1. [删除](http://zhidao.baidu.com/link?url=B8XTgGDuW1TDHJFiW-5QQKdB6gZCW1bZwF--GX43_MyyWIJ3SiMJam_EzqXknouCXv6xhOxxomqPm6iG2nxuLTBGpp63URsay6PL2e9Rkiu)
	* 编辑.gitmodules,删除对应要删除的submodule的行．
	* 编辑.git/config,删除有对应要删除的submodule的行．
	* 删除命令: git rm --cached bundle/xxxx (PS:此处最后没有符号 / .)
	* 删除对应的目录: rm -rf bundle/xxxx

###
