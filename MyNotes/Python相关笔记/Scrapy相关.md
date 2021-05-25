# Scrapy相关

## 环境搭建

*  **Windows安装**

```python
#前提要安装好Python，然后打开cmd，进入到相关的目录，创建Scrapy项目
#安装Scrapy
pip install scrapy

#创建Scrapy项目
scrapy startproject demo

#进入demo文件夹
cd demo

#创建一个spider,最后面接域名
scrapy genspider demospider baidu.com

#启动spider
scrapy crawl demospider
```

## Scrapy项目文件介绍

> |-demo  **项目名**
>
> ​	|-__init__.py          **初始化文件**
>
> ​	|-items.py      **存放items对象，相当于一个容器，和字典较像**
>
>  ​	|-middlewares.py     **定义Downloader Middlewares(下载器中间件)**
>
> ​	|-pipelines.py         **定义Item Pipeline的实现，实现数据的清洗，储存，验证。**
>
>  ​	|-settings.py			**用于设置Scrapy项目爬虫的相关信息（全局配置）**
>
>  ​	|-spiders				**spider爬虫文件**
>
>   		|-__init__.py			**初始化文件**
>
>  ​		 |-demospider.py    **名称为demospider.py的Spider爬虫文件**
>
>   |-scrapy.cfg     **配置文件**

 