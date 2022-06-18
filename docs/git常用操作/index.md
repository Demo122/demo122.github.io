# Git常用操作


## 1. git入门
学习了git使用，搭建了docsify个人博客，主要用于记录学习过程，Java笔记
其中git使用常用命令为：

```
git init  初始化仓库 
git add xxx.xx 添加文件
git add -A 添加所有修改的文件
git commit -m "xxxx"
git reset --hard HEAD^ 返回上一版本，HEAD时当前版本 
git branch xxx 创建分支
git checkout xx 切换分支
git merge xx 合并分支
git push origin xx 更新到github
git status 查看当前状态
....
```
附上参考资料，[不会再查](https://mp.weixin.qq.com/s/swnwBiuyVmhs5iPqv3H6BQ)

## 2. git开发实践
### 2.1 版本回退
####  2.1.1 回退至上一个版本

```shell
git reset --hard HEAD
```

####  2.1.2 对push的版本进行回退


