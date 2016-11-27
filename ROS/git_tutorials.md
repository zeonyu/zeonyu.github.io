# git 常用命令整理

note：详细教程请参考[廖雪峰老师的git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)，写的非常详尽，必读。然而，我们平时使用的时候，都是按照情景来选择需要使用的命令，这个时候很多命令容易忘记，为了使得今后可以快速的查阅，这里讲一些情景的命令进行整理。

## situation1

情景：云端创建一个新的repo，本机已经有代码和数据，希望把两个库同步。

操作：

1. 本机进行repo初始化及关联

```
git init
git add FILE_NAME
git commit -m "COMMIT ON IT"
git status
git diff FILE_NAME
git remote add origin git@github.com:zeonyu/zeonyu.github.io.git
git push -u origin master
```