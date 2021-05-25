# GitBook相关

## 一.安装Gitbook实现本地预览

*  **先下载 *nodejs* 并安装**

  * **Nodejs官网:**https://nodejs.org/en


![img](https://ae01.alicdn.com/kf/H5e8279b8cfc94e41ba6f162ce8210106v.jpg)

* **打开Node.js command prompt 或者git bash**

```python
npm install gitbook-cli -g  
gitbook -V   #如果有版本显示则安装gitbook成功

#创建存放gitbook文件夹(notes)并进入到相应的目录
gitbook init
gitbook serve  #用于本地搭建，如果要用于生产环境可不执行

gitbook build #其实在serve时就已经生成了对应的目录,默认为_book
gitbook build . book #前面路径是以当前路径为构建目录,后者路径是生成路径，上面和这个命令选一条执行
```

* **在notes文件夹中创建笔记文件夹(MyNotes),在MyNotes创建相应的笔记文件**

  > **使用gitbook init后会自动生成两个文件README.md和SUMMARY.md**
  >
  >**README.md使用过git的都知道这个文件**
  >  **SUMMARY.md就是自己要写文章章节目录**

* **编辑SUMMARY.md**

> **[]  中括号里面写笔记名字**
>
> **()  括号后面写笔记文件路径**
>
> **比如:**
>
> **在notes(当前文件夹)的MyNotes下创建aa.md:       \[aa\](MyNoyes/aa.md)**
>
> **在notes(当前文件夹)的MyNotes下创建bb文件夹且在文件夹里面添加aa.md: \[aa\](MyNoyes/bb/aa.md)**


```python
# 目录，根据自己创建的文件夹而定,在SUMMARY.md文件中添加以下内容:

* [前言](README.md)
* [第一章](Chapter1/README.md)
  * [第1节：衣](Chapter1/衣.md)
  * [第2节：食](Chapter1/食.md)
  * [第3节：住](Chapter1/住.md)
  * [第4节：行](Chapter1/行.md)
* [第二章](Chapter2/README.md)
* [第三章](Chapter3/README.md)
* [第四章](Chapter4/README.md)
```

* **配置目录折叠**

```python
npm install -g gitbook-plugin-splitter
gitbook install

在主目录添加book.json(需要手动创建)
{
    "plugins":[
            "expandable-chapters"
    ]
}
```

* **当我们编写完笔记之后，则重新生成对应的book文件夹**

```bash
gitbook build . book
```

* **若要本地预览**

```bash
gitbook server
```

## 二.展示到Github的page中

* **在Github中创建名为 用户名.github.io 的仓库**

* **在 用户名.github.io  的项目里点击 *settings* ，往下翻找到 *GitHub Pages***

![img](https://ae01.alicdn.com/kf/Hf65a6894a0db473caa245620152055d7V.jpg)

* **根据具体情况选择分支，一般选择master分支**

![img](https://ae01.alicdn.com/kf/Ha8a8a7b2763648ff996aff78113baa4ej.jpg)

* **将编译好的文件 *PUSH* 到远端仓库(进入到_book目录或者book目录)**

```python
git add .
git commit -m "Inital commit"
git remote add origin https://github.com/xxxx/xxx.git 
git push -u orgin master -f
```

## 三.更新

* **将原来的_book目录下的.git 隐藏文件复制到根目录（没有则忽略）**

- **在笔记文件夹中添加相关的笔记之后重新执行gitbook命名生成对应的_book目录**

```python
#不是在执行gitbook生成的_book目录下执行命令，而是最开始的笔记文件夹,有README.md文件的目录
gitbook build . book  
#此时会生成book文件夹，这个文件夹根据自己情况设置，然后我们可以重新执行git命令上传到github
```

* **进入到notes文件夹**

  **重新创建.git文件夹**

```bash
git init
git add .
git commit -m "notes"
git remote add notes https://github.com/用户名/用户名.github.io.git
#提交时,可以先使用git pull再git push
git pull notes
git push -u notes master

#或则不执行上面两句，直接执行
git push -u notes master -f #强制覆盖原来的github中的所有文件
```

**或者将刚刚复制的.git隐藏文件复制到此目录下**

```bash
#上面命令作废,执行下列命令
git add .
git commit -m "notes"
git pull origin
git push
```

## 四.常用插件

* https://www.jianshu.com/p/427b8bb066e6
* https://www.cnblogs.com/mingyue5826/p/10307051.html#autoid-2-3-0