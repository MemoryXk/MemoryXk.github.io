# Flask相关

## Flask-SQLalchemy

### 蓝本（蓝图）解释

为了在一个或多个应用中，使应用模块化并且支持常用方案， Flask 引入了 *蓝图* 概念。蓝图可以极大地简化大型应用并为扩展提供集中的注册入口。 [`Blueprint`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.Blueprint) 对象与 [`Flask`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.Flask) 应用对象的工作方式类似，但不是一 个真正的应用。它更像一个用于构建和扩展应用的 *蓝图* 。

> #### 通俗易懂的解释

 当我们写route路由的时候，一般会有很多个路由，甚至分前端路由和后端路由，当发生错误时，如果把这些路由写在一个文件中会很难找到错误的原因，所有这里就要用蓝本。

我们可以创建多个python文件（比如：前端，后端），在每个文件中导入Blueprint（蓝本），然后利用蓝本去写路由，最后通过蓝本把所有的路由进行合并。

有人会提出：直接导包不就行了吗？

导包可以，但是不能把所有的路由进行合并！

> #### 为什么使用蓝图？

**Flask 中蓝图有以下用途：**

- 把一个应用分解为一套蓝图。这是针对大型应用的理想方案：一个项目可以实例化 一个应用，初始化多个扩展，并注册许多蓝图。
- 在一个应用的 URL 前缀和（或）子域上注册一个蓝图。 URL 前缀和（或）子域的 参数成为蓝图中所有视图的通用视图参数（缺省情况下）。
- 使用不同的 URL 规则在应用中多次注册蓝图。
- 通过蓝图提供模板过滤器、静态文件、模板和其他工具。蓝图不必执行应用或视图 函数。
- 当初始化一个 Flask 扩展时，为以上任意一种用途注册一个蓝图。

> #### 蓝图的概念

**在蓝图被注册到应用之后，所要执行的操作的集合。当分配请求 时， Flask 会把蓝图和视图函数关联起来，并生成两个端点之前的 URL 。**

### 1.新建Flask可视化项目

> **项目命名为ViewData， 项目中新建文件夹app/models存放数据库映射类，app/static/js存放js的外部工具包，app/templates/main存放html前端页面，app/templates/errors存放404之后的html前端页面，app/views存放SQLAlchemy的语法的数据库访问类。**

![img](https://ae01.alicdn.com/kf/H01c5b20f316347d6aa37e55a664bb907v.jpg)

### **2.新建app/__init__.py**



> **新建flask的初始化类，自定义函数def  create_app(config_name)封装一个方法，专门用于创建Flask实例；自定义函数def  config_errorhandler(app)编写访问失败之后的404页面的跳转设置。**

```python
from flask import Flask, render_template
from app.config import config
from app.extensions import config_extensions
from app.views import config_blueprint

# 封装一个方法，专门用于创建Flask实例
def create_app(config_name): # development
    # 创建应用实例
    app = Flask(__name__)
    # 初始化配置
    app.config.from_object(config.get(config_name) or config['default'])
    # 调用初始化函数
    config[config_name].init_app(app)
    # 配置扩展
    config_extensions(app)
    # 配置蓝本
    config_blueprint(app)
    # 错误页面定制
    config_errorhandler(app)
    # 返回应用实例
    return app


def config_errorhandler(app):
    # 如果在蓝本定制，只针对本蓝本的错误有效，
    # 可以使用app_errorhandler定制全局有效的错误显示
    @app.errorhandler(404)
    def page_not_found(e):
        return render_template('errors/404.html')
```

### **3.新建app/config.py**

> **新建flask连接数据库的配置类，将flask框架连接数据库的配置编写成函数，包括数据库的ip、端口、用户名、密码等。**

```python
import os

base_dir = os.path.abspath(os.path.dirname(__file__))


# 通用配置
class Config:
    # 秘钥
    SECRET_KEY = os.environ.get('SECRET_KEY') or '123456'
    # 数据库
    SQLALCHEMY_COMMIT_ON_TEARDOWN = True
    SQLALCHEMY_TRACK_MODIFICATIONS = False

    # 额外的初始化操作，即使什么内容都没有写，也是有意义的
    @staticmethod
    def init_app(app):
        pass


# 开发环境   语法：mysql+pymysql://用户名：密码@ip：端口/数据库名
class DevelopmentConfig(Config):
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:root@localhost:3306/visiondata'


# 测试环境
class TestingConfig(Config):
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:root@localhost:3306/visiondata'


# 生产环境
class ProductionConfig(Config):
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:root@localhost:3306/visiondata'


# 配置字典
config = {
    'development': DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig,

    'default': DevelopmentConfig
}
```

### **4.新建app/ extensions.py**

> **导入flask相关模块，创建SQLAlchemy对象并初始化Bootstrap。自定义函数def config_extensions(app)完成SQLAlchemy和Bootstrap的初始化。**

```python
# 导入类库
from flask_bootstrap import Bootstrap
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from flask_moment import Moment


# 创建对象
bootstrap = Bootstrap()
db = SQLAlchemy()
moment = Moment()
migrate = Migrate(db=db)



# 初始化
def config_extensions(app):
    bootstrap.init_app(app)
    db.init_app(app)
    moment.init_app(app)
    migrate.init_app(app)
```

### **5.新建app/models/表名.py**

> **编写flask的数据库表的映射类，每个表格对应一个文件，映射类命名与表名一致。**
>

**表hot_work**

```python
from app.extensions import db


class HotWork(db.Model):
    __tablename__ = 'hot_work'
    id = db.Column(db.Integer, primary_key=True)
    job_name = db.Column(db.String(255))
    job_number = db.Column(db.Integer)
```

**表bigdata_work**

```python
from app.extensions import db


class BigDataWork(db.Model):
    __tablename__ = 'bigdata_work'
    id = db.Column(db.Integer, primary_key=True)
    job_name = db.Column(db.String(255))
    company_name = db.Column(db.String(255))
    city = db.Column(db.String(255))
    job_require = db.Column(db.Text)
    recruit_number = db.Column(db.String(255))
    money = db.Column(db.String(255))
    skill_require = db.Column(db.String(255))
    release_date = db.Column(db.String(255))
    sex = db.Column(db.String(255))
    company_detail = db.Column(db.String(255))
    education = db.Column(db.String(255))
```

**表avg_money_city**

```python
from app.extensions import db


class AvgMoneyCity(db.Model):
    __tablename__ = 'avg_money_city'
    id = db.Column(db.Integer, primary_key=True)
    city = db.Column(db.String(255))
    avg_money = db.Column(db.String(255))
```

**表avg_money_bigdata**

```python
from app.extensions import db


class AvgMoneyBigData(db.Model):
    __tablename__ = 'avg_money_bigdata'
    id = db.Column(db.Integer, primary_key=True)
    city = db.Column(db.String(255))
    avg_money = db.Column(db.String(255))
```

### **6.新建app/models/__init__.py**

> **编写flask的访问数据库的初始化文件，导入数据库映射表类。**
>

```python
from app.extensions import db

from .hot_work import HotWork
from .bigdata_work import BigDataWork
from .avg_money_city import AvgMoneyCity
from .avg_money_bigdata import AvgMoneyBigData
```

### **7.新建app/static/js**

> **将html前段需要用到的js工具包存放在js文件夹下**

**Echarts官网：**https://www.echartsjs.com/zh/index.html

**Echarts镜像网站：**https://cdn.bootcss.com/echarts/4.0.4/echarts.min.js  （4.0.4是版本号）

**JQuery官网：**https://jquery.com

### **8.新建ViewData/mamage.py**

> **新建flask启动类，创建flask实例和数据库迁移命令，以及程序的入口。**
>

```python
import os

from flask_migrate import MigrateCommand
from flask_script import Manager, Server

from app import create_app

# 获取配置
config_name = os.environ.get('FLASK_CONFIG') or 'default'
# 创建Flask实例
app = create_app(config_name)

# 创建命令行启动控制对象
manager = Manager(app)

manager.add_command("runserver", Server(use_debugger=True))

# 添加数据库迁移命令
manager.add_command('db', MigrateCommand)

# 启动项目
if __name__ == '__main__':
    manager.run()
```

### **9.新建app/views/__init__.py** 

> **新建flask的蓝本配置初始化类，进行蓝本的配置，自定义函数def config_blueprint(app)以及封装函数完成蓝本的注册。**
>

```python
from .main import main


# 蓝本配置
DEFAULT_BLUEPRINT = (
    # 蓝本，前缀
    (main, ''),
)


# 封装函数，完成蓝本注册
def config_blueprint(app):
    for blueprint, prefix in DEFAULT_BLUEPRINT:
        app.register_blueprint(blueprint, url_prefix=prefix)
```

### **10.新建app/views的main.py**

(1)根据数据库表格hotwork，分析并统计招聘数量最多的前十名热门职位,分别使用柱状图表达

**数据源：**数据库visiondata中hot_work表

**在app/views下的main.py添加函数def get_hot_work() ，使用SQLAlchemy语法编写sql的查询语句，查询统计招聘数据前十的热门职位。**

```python
#  统计招聘数量最多的前十热门职位  获取结果：
def get_hot_work():     # 编写查询语句  返回前10条
    # sql: select * from hot_work group by job_name limit 10
    ghw_rs = HotWork.query.order_by(desc('job_number')).limit(10)
    print(ghw_rs)
    return ghw_rs
```

(2)请根据指定表中的数据，统计出全国某些城市指定招聘岗位平均工资，通过南丁格尔玫瑰图进行呈现。

**数据源：**数据库visiondata中avg_money_city和avg_money_bigdata

**在app/views下的main.py添加函数def get_avg_money_city()和def get_avg_money_BigData()，使用SQLAlchemy语法编写sql的查询语句，查询两个表中的城市平均工资数据。**

```python
# 所有城市招聘数据的平均工资     获取结果
def get_avg_money_city():
    # sql: select * from avg_money_city
    gamc_rs = AvgMoneyCity.query.all()
    print('所有城市招聘数据的平均工资     获取结果成功')
    return gamc_rs


# “大数据”相关职位所有城市招聘数据的平均工资       获取结果
def get_avg_money_BigData():
    # sql: select * from avg_money_big_data
    gamb_rs = AvgMoneyBigData.query.all()
    print('“大数据”相关职位所有城市招聘数据的平均工资       获取结果成功')
    return gamb_rs
```

(3)分析并统计招聘数量"大数据"相关职位招聘数量,分别使用柱状图表达，同时在网页后台输出相关数据打印语句。

**数据源：**数据库visiondata中bigdata_work

**在app/views下的main.py添加函数def get_big_data_work()，使用SQLAlchemy语法编写sql的查询语句，对相同职位进行数量汇总。**

```python
# 对相同职位进行数量汇总，"大数据"相关职位招聘数量比较
def get_big_data_work():
    # sql: select job_name,count(*) from bigdata_work where job_name like '%大数据%' GROUP BY job_name
    gbdw_rs = db.session.query(BigDataWork.job_name, func.count('*').label('job_count'))\
        .group_by(BigDataWork.job_name).order_by(desc('job_count')).all()
    print('"大数据"相关职位招聘数量：' + str(gbdw_rs))
    return gbdw_rs
```

**app/views下的main.py完整代码，如下所示：**

```python
from app.extensions import db
from app.models import HotWork, BigDataWork, AvgMoneyCity, AvgMoneyBigData
from flask import Blueprint, render_template
from sqlalchemy import *

main = Blueprint('main', __name__)  # 实例化路由


@main.route('/')
def index():
    return render_template('/main/echarts.html')    # 当url访问 / 直接跳转到主页


@main.route('/index/')      # url访问 /index/ 跳转主页  调用display方法  将查询结果通过render_template()传入html页面
def display():
    rs_ghw = get_hot_work()
    rs_gbdw = get_big_data_work()
    rs_gamc = get_avg_money_city()
    rs_gamb = get_avg_money_BigData()
    return render_template('/main/echarts.html', rs_ghw=rs_ghw, rs_gbdw=rs_gbdw, rs_gamc=rs_gamc, rs_gamb=rs_gamb)


#  统计招聘数量最多的前十热门职位  获取结果：
def get_hot_work():     # 编写查询语句  返回前10条
    # sql: select * from hot_work group by job_name limit 10
    ghw_rs = HotWork.query.order_by(desc('job_number')).limit(10)
    print(ghw_rs)
    return ghw_rs


# 所有城市招聘数据的平均工资     获取结果
def get_avg_money_city():
    # sql: select * from avg_money_city
    gamc_rs = AvgMoneyCity.query.all()
    print('所有城市招聘数据的平均工资     获取结果成功')
    return gamc_rs


# “大数据”相关职位所有城市招聘数据的平均工资       获取结果
def get_avg_money_BigData():
    # sql: select * from avg_money_big_data
    gamb_rs = AvgMoneyBigData.query.all()
    print('“大数据”相关职位所有城市招聘数据的平均工资       获取结果成功')
    return gamb_rs


# 对相同职位进行数量汇总，"大数据"相关职位招聘数量比较
def get_big_data_work():
    # sql: select job_name,count(*) from bigdata_work where job_name like '%大数据%' GROUP BY job_name
    gbdw_rs = db.session.query(BigDataWork.job_name, func.count('*').label('job_count'))\
        .group_by(BigDataWork.job_name).order_by(desc('job_count')).all()
    print('"大数据"相关职位招聘数量：' + str(gbdw_rs))
    return gbdw_rs
```

### **11.新建app/templates/errors/404.html**

> **编写url访问失败之后的提示页面。** 
>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
404
</body>
</html>
```

### **12.新建app/templates/main/echarts.html**

> **编写js获取到flask传过来的数据，通过echarts组件进行结果的展示（包括css样式）。**
>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>招聘信息统计</title>
    <script type="text/javascript" src="/static/js/echarts.js"></script>
    <script type="text/javascript" src="/static/js/jquery.min.js"></script>
    <script type="text/javascript" src="/static/js/js2wordcloud.js"></script>
</head>
<style>
    html , body , .content {
    width:100%;
    height:100%;
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    background-color: #ccc;
}
.content {
    padding: 40px;
}
.header {
    height: 10%;
    width: 100%;
    font-size: 24px;
    font-weight: 700;
    line-height: 60px;
    text-align: center;
}
.body {
    height: 100%;
    width: 100%;
    text-align: center;
}
.chartBox {
    width: 100%;
    height: 60%;
    margin-bottom:40px;
}
</style>
<body>
    <div class="content">
        <div class="header"></div>
        <div class="body">
            <div class="chartBox" id="hotWork"></div>
            <div class="chartBox" id="bigDataWork"></div>
            <div class="chartBox" id="pieAvgMoneyCity"></div>
        </div>
    </div>
</body>
<script>
    //折线图   招聘数量最多的前十热门职位
    var hotWork = echarts.init(document.getElementById('hotWork'));     //获取div的id   实例化echarts组件
    var data_name = [{% for r in rs_ghw %} "{{r.job_name}}", {% endfor %}]     //将职位名job_name存放在一个数组中作为x轴数据
    var data_y = [{% for r in rs_ghw %}"{{r.job_number}}",{% endfor %}]     //将职位数量job_number存放在一个数组中作为y轴数据

    console.log('十大热门职位:' + data_name);
    console.log('数据分别为:' + data_y);
    console.log('最大值: ' + Math.max.apply(null, data_y) + '，    最小值:' + Math.min.apply(null, data_y));

    workOption = {
        title: {
            text: '职位分析',   //主标题
            subtext: '            ---10大热门职位分析',  //副标题
            x: '45%'    //设置标题位置
        },
        xAxis: {
            type: 'category',
            name: '岗位名称',
            data: data_name,
            axisLabel : {
                interval: 0,
                rotate: "25"    //x轴字体的旋转度
            }
        },
        yAxis: {
            name: '招聘数量',
            type: 'value'
        },
        series: [{
            data: data_y,
            type: 'line',   //设置图形为折线图
            label: {
                normal: {
                    show: true,
                    position: 'top'  //折线图顶部显示对应的x轴数值
                }
            }
        }]
    };
    hotWork.setOption(workOption);   //设置echarts的option参数  加载并显示图形

    //南丁格尔玫瑰图
    var pieAvgMoneyCity = echarts.init(document.getElementById('pieAvgMoneyCity'));

    var city = [{% for r in rs_gamc %}"{{r.city}}",{% endfor %}]
    var avg_money_pie_city =  [{% for r in rs_gamc %}{value:"{{r.avg_money}}", name:"{{r.city}}"},{% endfor %}]
    var avg_money_pie_bigdata =  [{% for r in rs_gamb %}{value:"{{r.avg_money}}", name:"{{r.city}}"},{% endfor %}]
    var avg_money_city = [{% for r in rs_gamc %} "{{r.avg_money}}", {% endfor %}]
    var avg_money_big_data = [{% for r in rs_gamb %}"{{r.avg_money}}",{% endfor %}]
    var dataInt_city = [];
    var dataInt_big_data = [];

    dataInt_city.forEach(function(data){
        avg_money_city.push(+parseInt(data));   ///遍历数组   将每个元素变成整型
    });
    dataInt_big_data.forEach(function(data){
        avg_money_big_data.push(+parseInt(data));   ///遍历数组   将每个元素变成整型
    });

    console.log('所有城市平均薪资:' + city);
    console.log('数据分别为:' + avg_money_city);
    console.log("所有城市最大平均薪资为" + Math.max.apply(null, avg_money_city));
    console.log("“大数据”相关职位城市招聘数据的平均工资" + city);
    console.log('数据分别为:' + avg_money_big_data);
    console.log("“大数据”相关职位最大平均薪资为" + Math.max.apply(null, avg_money_big_data));

    pieAvgMoneyCityOption = {
        title : {
            text: '所有城市招聘数据的平均工资 vs “大数据”相关职位所有城市招聘数据的平均工资',
            subtext: '南丁格尔玫瑰图',
            x:'center'
        },
        tooltip : {
            trigger: 'item',
            formatter: "{b}:{c}({d}%)"      //当鼠标移动到图形 显示数据(格式): 佛山（1111） 10%
        },
        legend: {
            x : 'center',
            y : 'bottom',
            data:city
        },
                color:[
            '#C1232B','#B5C334','#FCCE10','#E87C25','#27727B',
                             '#668ffe','#00ca54','#00dbfa','#f3006a','#60C0DD',
                             '#d714b7','#84433c','#f490f3','#000000','#26C0C0'],
        series : [
            {
                type:'pie',
                radius : [20, 110],     //图像的大小
                center : ['25%', '50%'],    //图形的位置
                roseType : 'radius',        //南丁格尔玫瑰图的参数
                data:avg_money_pie_city
            },
            {
                type:'pie',
                radius : [30, 110],
                center : ['75%', '50%'],
                roseType : 'area',
                data:avg_money_pie_bigdata
            }
        ]
    };
    pieAvgMoneyCity.setOption(pieAvgMoneyCityOption);


    //柱状图   "大数据"相关职位招聘数量
    var bigDataWork = echarts.init(document.getElementById('bigDataWork'));

    var job_name = [{% for r in rs_gbdw %} "{{r.job_name}}", {% endfor %}]
    var quantity = [{% for r in rs_gbdw %}"{{r.job_count}}",{% endfor %}]  // 获取元组中的第二个元素('AI大数据工程师', 1095)

    console.log('招聘职位:  ' + job_name);
    console.log('对应职位招聘数量:  ' + quantity);
    console.log('岗位需求量最大:  ' + Math.max.apply(null, quantity) + '，  岗位需求量最少：' +  Math.min.apply(null, quantity));
    bigdataworkOption = {
        title: {
            text: '大数据相关职位招聘数量',
            subtext: '               ----职位招聘对比',
            x: '45%',
            //modified 0523
            textStyle:{
                //文字颜色
                color:'#f30008',
                //字体风格,'normal','italic','oblique'
                fontStyle:'oblique',
                //字体粗细 'normal','bold','bolder','lighter',100 | 200 | 300 | 400...
                fontWeight:'bold',
                //字体系列
                fontFamily:'FangSong'
                //fontFamily: 'KaiTi'
                //字体大小
                //fontSize:18
            }
            //modified end
        },
        xAxis: {
            type: 'category',
            name: '职位名称',
            data: job_name,
            axisLabel : {
                interval: 0,
                rotate: "15"    //x轴字体的旋转度
            }
        },
        yAxis: {
            type: 'value',
            name: '招聘数量'
        },
        series: [{
            data: quantity,
            type: 'bar',
            label: {
                normal: {
                    show: true,
                    position: 'top'
                }
            },
            //modified0523
            itemStyle: {
               normal: {color: 'black'}
            }
            //modified end
        }]
    };
    bigDataWork.setOption(bigdataworkOption);

</script>

</html>
```

### **13.运行Flask程序查看结果**

**（1）分析并统计招聘数量最多的前十名热门职位,分别使用折线图表达**

![img](https://ae01.alicdn.com/kf/Hf41ba6de29644c34854febf90f1832be5.jpg)

**（2）分析并统计招聘数量"大数据"相关职位招聘数量,分别使用柱状图表达。**

![img](https://ae01.alicdn.com/kf/H24e655fbc977426f927cba15178571a13.jpg)

**（3）统计出全国某些城市指定招聘岗位平均工资，通过南丁格尔玫瑰图进行呈现。**

![img](https://ae01.alicdn.com/kf/Hccadf8fd4970402db4925f121db9e949F.jpg)

### 语法

db.create_all() ：创建实体表
db.drop_all(): 删除表

**1）插入表**
Db.session.add(user) #user是实体对象
\##批量插入
Db.session.add_all([user1，user2，user3,…..])
\##提交
Db.session.commit()

**2) 修改表**
如果实体有id，那么就会变成更新操作，如果没有就是默认插入操作
User.name=’xiaobing’
Db.session.add(user)
Db.session.commit()

**3)删除行**
Db.session.delete(user)
Db.session.commit()


**2,查询**
**1）filter_by,filter**
User.query.filter_by(role=user_role).all()
user_role = Role.query.filter_by(name='User').first()
filter_by是直接根据实体对象去转化sql,filter是比较直接插入到sql中

常用的SQLALchemy查询过滤器
过滤器 说明:

* filter() 把过滤器添加到原查询上
* filter_by() 把等值过滤器添加到原查询上
*  limit() 限制原查询返回的结果数量
*  offset() 偏移原始查询返回的结果
* order_by() 根据指定条件对原查询进行排序
* group_by() 根据指定条件对原查询结果进行分组

```python
在users = db.relationship('User', backref='role', lazy='dynamic') 加入了lazy=’dynamic’参数，从而禁止自动执行查询，user_role.users会返回一个尚未执行的查询，因此可以在其上添加过滤器
user_role.users.order_by(User.username).all()
最常用的SQLALCHEMY列选项
选项名 说明
primary_key 主键
unique 是否允许重复值
index 设为True，为这一 列建立索引
nullable 设为True，允许使用空
default 默认值
```

**2）like 模糊匹配**
例如：
categorys = Category.query.filter(Category.name.like("%"+keyword+"%")).all()
这种好像也可以
hosts.query.whoosh_search('ce').all()

**3）连表查询**

```python
class Topic(db.Models):
__tablename__ = 'topic'
id = db.Column(db.Integer, primary_key = True)
content = db.Column(db.Text)

class Reply(db.Models):
__tablename = 'reply'
id = db.Column(db.Integer, primary_key = True)
topic_id = db.Column(db.Integer)
content = db.Column(db.Text)
```

查询：

```python
result=Reply.query.join(Topic, Reply.topic_id==Topic.id).add_entity(Topic).all()
```

然后遍历值：

```python
for res in result：
   res.Reply.xxx
   res.Topic.xxx
```

**4）查询返回指定的字段**

```python
User.query(User.name,User.email,User.sex).order(User.name.des()).limit(20).skip(200)
```

**5)group by 分组查询**

第一种方式：

```python
rs =User.query(StudentInfo.cls, func.sum(StudentInfo.cert_count)).group_by(StudentInfo.cls).all()
```

第二种方式：

```python
Create_engine=SQLAlchemy.get_engine()
sql = 'select cls, sum(cert_count) from stuinfo group by cls'
rs = engine.execute(sql)
for row in rs:
      print row[0], row[1]
```

**6) 分页显示paginate**

```python
pagination = Post.query.order_by(Post.timestamp.desc()).paginate(page,per_page=current_app.config['ARTISAN_POSTS_PER_PAGE'],error_out=False)
```

**返回的pagination对象 包含以下：**

* Page：当前页
* Page_per:每页显示多少条
* Total:总条数

