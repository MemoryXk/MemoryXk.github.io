# Git相关

## 一.第一次上传本地项目到GitHUb

* **1.下载Git软件**
  * **Git官网：https://git-scm.com/**

![img](https://ae01.alicdn.com/kf/H73d9ada8d66f498eb2668ea35eec12e2T.png)

* **2.上传**

```bash
#进入到指定位置或者在指定位置打开Git Bash
mkdir note
cd note
git init
git add .
git commit -m "note"  #会报错，提示输入账户和名字
```
* 第一次上传的时候需要配置GitHub的账号和密码

![img](https://ae01.alicdn.com/kf/Hfd6fb782dacc4f4988a5622656384404O.png)

```python
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git commit -m "note"
git remote add origin https://github.com/xxxx/xxx.git   
git push -u origin master
#创建GitHub仓库时会有一个链接
```

![img](https://ae01.alicdn.com/kf/Ha20126b83a8040ce969beef7a01d12ecC.png)

## 二.更新

```python
#更新完文件之后，执行以下命令
git add .
git commit -m "commit"
git push
```

