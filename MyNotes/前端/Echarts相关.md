# Echarts相关

> **生成Echarts对象绑定标签：**
>
> **ec = echarts.init(document.getElementById('hotwork'))**

> **设置echarts属性：**
>
> **ec.setOption(workOption)          workOption为图形的属性**

## 折线图(line)

```javascript
workOption= {
	title:{                 //标题
		text:"职位分析",      //主标题
		subtext:"--10大热门职位分析",    //副标题
		x:'45%'，                  //标题位置，可以设置center，right等或者百分比
		textStyle:{                  //标题样式
			color:#000000
		}
 	},
 	xAxis:{					//x轴的属性
 		type:'category',	//x轴坐标类型，category类目轴，value数值轴，time时间轴
 		data:[],				//x轴数据，列表
 		name:"时间",			//x轴名字（标签）
   
        axisLabel:{			//设置x轴数据的一些属性
        	interval:0,		//设置间隔,0表示强制显示所有的标签，1表示隔一个标签显示一个标签，2表示							  隔两个标签显示一个标签，以此类推
        	rotate:'25'		//x轴数据的倾斜度，逆时针旋转，-25是顺时针旋转
        }
 	},
 	yAxis:{		    //属性基本上和xAxis一样		
 		name:"摄氏度°C",
 		type:'value'
 	},
    legend:{					//设置图列
      	data:['折线图'],				//这里设置series中的name，注意和数据项轴（饼图等）不一样
        left:'right'			//设置图例的位置 	
    },
 	series:[		//设置图形的属性,这里是一个列表包起来，因为一个图中可以画很多个图形
 		{
            name:'折线图',
 			type:'line',	//设置图形类型为折线图
 			data:[],			//data是x轴数据对应的y轴数据
 			label:{				//显示数据并且设置显示的位置
                show:true,
                position:top		//在顶部显示数据
            }
 		}
 	],
    tooltip:{ 			//设置鼠标放到某个点的时候，会出现相应的x,y数据
        show:true
    }
}
```

![img](https://ae01.alicdn.com/kf/H5ff2f22d965345fb95d0cbb892ec5478z.jpg)

## 柱状图&堆叠柱状图

```javascript
barOption={
    title:{
        text:'大数据相关职位招聘数量',
        subtext:'--职位招聘对比'，
        x='center'
    },
    xAxis:{
        type:'category',
        name:'职位名称',
        data:['大数据开发','爬虫工程师','大数据运维'，'可视化工程师'],
        axisLabel:{
            interval:0,
            rotate:'-25'
        }
    },
    yAxis:[          //如果有多个y轴，则添加两个，以列表包起来，默认显示是左右两边有两条y轴
        {
       		name:'招聘数量',
       		type:'value'
    	},
        {
            name:'测试',
            interval:5,           //每一个数据相隔多少
            min:1,			//设置最小值
            max:50			//设置最大值
        }
    ],
    tooltip:{
        show:true,
        trigger:'axis'
    },
    legend:{
        show:true,
        data:['长沙','武汉']
    },
    series:[
        {
            name:'长沙'
            type:'bar',
            stack:'城市',    //stack可以当作是一个组，如果是一个组的话，每个组中的柱状会上下堆叠
            data:[100,200,300,400],
    		label:{
               	normal:{
                    show:true,					//显示数据文本在图上
                	position:'insideRigt'			//设置显示的位置，inside是里面
                }
            }，
    		itemStyle:{
    			normal:{
    				color:'blue'			//设置每个柱形的颜色
				}    			
			}
        },
        {
        	name:'武汉',
            stack:'城市',
            type:'bar',
            data:[500,600,700,800],
        }
    ]
}
```

![img](https://ae01.alicdn.com/kf/H6430482d3c054cd18402d7fd57f29d38O.jpg)

## 南丁格尔图

```javascript
bigdataOption={
    title:{
        text:"所有城市招聘数据的平均工资vs“大数据”相关职位所有城市招聘数据的平均工资",
        subtext:'南丁格尔玫瑰图',
        x:'center',
    },
    tooltip:{
        show:true,
        trigger:'item'，				//触发的类型，item是数据项的图形触发，axis坐标轴触发
        formatter:{b}:{c}:{d}%,             //设置显示的格式，{b}数据名称，{c}数值，{d}百分比的值
    },
    legend:{					//设置图列
      	data:['长沙','武汉','成都']，		//这里设置series中data数据列表中每一个字典的name值
    	left:'right'
    },
    color:  						//设置图形中有多少颜色
 		[
           '#C1232B','#B5C334','#FCCE10','#E87C25','#27727B',
           '#668ffe','#00ca54','#00dbfa','#f3006a','#60C0DD','#d714b7',
           '#84433c','#f490f3','#000000','#26C0C0'
        ],
     series:[
         {
             type:'pie',		//这里设置类型为pie饼图，南丁格尔图属于饼图的一种
             radius:[20,110],		//设置图形的大小，20是内圆半径，110是外圆半径
             center:['25%','50%'],		//设置图形的位置，25%是根据当前series宽度来判断，而50%根据高度来判断，相当于x和y的位置
             roseType:'radius',		//设置南丁格尔图，其中，radius类型是展现以百分比区分的扇区圆心角，且通过半径来显示，而area类型是指所有扇区的圆心角相同，根据半径来区分，只展现半径
             data:[{name:'长沙',value:2000},{name:'武汉',value:3000}]
         }，
         {
         	type:'pie',
        	radis:[20,110],
         	center:['75%','50%'],
            roseType:'area',
            data:[{name:'北京',value:5000},{name:'上海',value:8000}]
         }
     ]
}
```

![img](https://ae01.alicdn.com/kf/H2fefa9fd8dc84b359480e92b09e72989K.jpg)

## 雷达图

```javascript
radarOption={
	title:{
     	text:'维度',
        subtext:'雷达图'
    }
    radar:{
    	name:{        
    		textStyle:{				//设置维度名称的样式
    			color:'#fff',			//#fff白色
    			backgroundColor:'#999',		//#999灰色
    			borderRadius:3,				//圆角的半径
    			padding:[5,5,1,5]			//内边距[5,5,1,5]分别对应[上，右，下，左]
			}             
		},
        indicator:[                 //有多少维度，max可选，官方建议选上
            {name:'平均工资',max:20000},
            {name:'招聘数量',max:10000},
            {name:'产品',max:30000}
        ]
	},
    series:[
        {
            type:'radar',
            data:[                     //列表里面包含字典，字典中value对应着每个维度的值
                    {name:"长沙",value:[15000,8000,20]},
                    {name:"武汉",value:[10000,9000,30]}
                ]
        }
    ]
}
```

![img](https://ae01.alicdn.com/kf/H1e0068c17e4c400e86607dd397d85f833.png)

## 散点热力图

```javascript
scantterOption={
	title:{
		text:'各城市大数据相关职位招聘数量',
		subtext:'--散点热力图'
	},
	tooltip:{
		show:true,
		trigger:'item'
	},
	legend:{
		show:true,
		data:['长沙','武汉'],
		top:'bottom'，
		left:'right'
	},
	visualMap:{
		min:0,            //设置数据的最大和最小值
		max:300,
		splitNumber:5,			//把数据范围分成5个段
		inRange:{
			color: ['#50a3ba', '#eac736', '#d94e5d']    //设置散点的颜色
		}
	}，
	geo:{
		map:"china",
		label:{
			emphasis:{						//设置地图中部的标签和多边形的高亮显示
				show:true
			}
		}
	}，
	series:[
		{
			type:'scatter',
			coordinateSystem:'geo',			//设置成geo（地图）类型，地理坐标轴，cartesian2d二维的直角坐标系，polar极坐标系
			data:[
				{name:'长沙',value:20}
				{name:'武汉'3131}
			]
		}
	]
}
```

![img](https://ae01.alicdn.com/kf/Ha99097cad88d492b9eb86bb4636a0dd9b.jpg)

## 词云图

```javascript
//词云图
list=[['zookeeper','10000'],['hadoop','300000'],['hive','1656'],['spark','20000'],['sqoop','10000'],['hbase','25000'],['flume','23000'],['Kafka','28000']]
    var wordCloud = new Js2WordCloud(document.getElementById('wordCloud'));
    wordCloudOption = {
        tooltip: {
            show: true
        },
        list: list,
        color: '#15a4fa',   //字体颜色
        backgroundColor: '#fff',
        shape: 'circle',
        ellipticity: 1
    }
wordCloud.setOption(wordCloudOption);
```

![img](https://ae01.alicdn.com/kf/Hfd40fcdda6d849bdabfd0f593d7dd86cN.png)