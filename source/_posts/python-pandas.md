---
title: 玩转Pandas数据处理
date: 2020-05-03 00:32:22
tags: [python,pandas]
---

## 1、基本处理

- map仅能处理单列
- apply能处理单列或多列，如果直接对df.apply(func,axis=1),那么传入函数的是df,df['price'].apply(np.log)传入的是price列
- applymap对全部元素操作，df.applymap(lambda x:"%.2f" % x)

<!--more-->

```python
# 分组后直接apply，传入的是每个组的df
arpu =date_buy_3.groupby('date').apply(lambda x:x[x.behavior_type==4].total.sum()/len(x.user_id.unique()))

df.info() # 获取df的摘要，包括Index范围，所有列名，每一列非空值的数量及数据类型
df['len_str'] = df['grammer'].map(lambda x: len(x)) # 求每个字符串的长度

df.describe() # 观察这一系列数据的范围,只对可以运算的列有效。大小、波动趋势等等，便于判断后续对数据采取哪类模型更合适
# 包含计数，平均值，标准差，最小值，最大值，25%，50%，75%分位的数值

df.set_index("A") # 设置A列为索引
df[['ID','age']].astype(float) # 强制类型转换
df['price'] = pd.to_numeric(df['price']) # 将某列转换成数值类型
df['C'] = df['A']+df['C'] # 合并成新列
df["D"] = df["C"].map(str) + df['B'] # 此时C列是数字

df1.sex.value_counts() # sex列分值计数
df1.age.replace('不详',0,inplace=True) # 值替代
df1.age.astype(int) # 类型强制转换

df.people.diff() # 求前后的差值，默认为1(此时第一行会是NaN)
data['收盘价(元)'].pct_change() # 当前元素和之前元素相差的百分比，默认为1
df['people'].rolling(2).mean() # 2个为一组向下求平均值，有交错

# map处理某一列Series，apply处理一个DateFrame
def func(df):
    lst = df['salary'].split('-')
    smin = int(lst[0].strip('k'))
    smax = int(lst[1].strip('k'))
    df['salary'] = int((smin + smax) / 2 * 1000)
    return df
df = df.apply(func,axis=1) # 求平均工资的具体数值
df['sex']=df['sex'].map({'男':1,'女':0})

# map的函数只能接收一个参数
def apply_age(x,bias):
    return x+bias
#以元组的方式传入额外的参数
data["age"] = data["age"].apply(apply_age,args=(-3,))
df['工时']=df['工时']*df['工时']
df['工时']=df['工时']-3
df[['付费额度','计费点id','工时']].apply(np.log)
np.median(df['salary']) # 计算中位数
df.name = df.name.map(lambda x: x + '1班')  # lambda 表达式映射

df1.sort_values(by='comment',ascending=False)[:10] # 按评论数降序排列
df.sort_index(ascending = True) # 按索引升序排序
df2['province']=df2['home'].str.split('·').str[0] # 新建一列，只获取省份

data.groupby('sex')['favour'].sum() # 分男女查看，喜爱数的总数
pd.crosstab(index=data['sex'],columns=data['martial_status']) # 分男女查看，婚姻状况的状态人数，交叉表，用于计数

data.groupby('constellation')['qiushi'].sum() # 分星座查看，qiushi的发布量，agg：聚合
d1=data.groupby(['sex','constellation']).agg({'age':'mean'}) # 分男女查看不同星座下年龄的平均值
heros.role_assist.unique() # 去重后的数值
heros.role_assist.nunique() # 去重后的个数

g1=heros.groupby(['attack_range','role_main']).agg({'hp_max':'mean','hp_growth':'max'}) # 分组后对hp_max求平均值，对hp_growth求最大值

pd.crosstab(index=heros['role_main'],columns=heros['attack_range']) # 交叉表，用于计数

pd.pivot_table(data=heros,index='role_main',values='hp_max',columns='attack_range',aggfunc='count',fill_value=0) # 透视表，行筛选出'role_main'，再根据'attack_range'分组统计多少种'hp_max'

pd.pivot_table(data=heros,index='role_main',columns='attack_range',values='hp_max',aggfunc='mean',fill_value=0) # 结果表行是'role_main',列是'attack_range'，然后对'hp_max'求平均值
heros.groupby(['role_main','attack_range'])['hp_max'].mean()

df['col1'][~df['col1'].isin(df['col2'])] # 提取第一列中不在第二列出现的数字
df.style.format({'data': '{0:.2%}'.format}) # 格式化data列，23%
df['split'] = df['linestaion'].str.split('_') # 得出的是列表
df[df['industryField'].str.startswith('数据')] # 提取以数据开头的行
df.agg({"salary":np.sum,"score":np.mean}) # agg是聚合函数
```

**快速画图**

```python
df.people.plot(kind='pie') # 饼状图
df.people.plot(kind='hist') # 直方图
df.people.plot(kind='bar') # 柱状图
df.people.plot() # 折线图

data[['收盘价(元)','开盘价(元)']].plot()
```

**原始数据最基本的操作一定包括如下三步**

1. 空值的处理
2. 重复值的处理
3. 异常值的处理

## 2、删除重复元素

使用duplicated()函数检测重复的行，返回元素为布尔类型的Series对象，每个元素对应一行，如果该行不是第一次出现，则元素为True

- 使用drop_duplicates()函数删除重复的行
- 使用duplicated()函数查看重复的行

```PYTHON
df = DataFrame(data={
    "name":["lucy","tom","jack","tony","mery","rose","black"],
    "python":np.random.randint(0,100,size=(7)),
    "java":np.random.randint(0,100,size=7),
    "php":np.random.randint(0,100,size=7)
})

df.loc[df.duplicated(keep='last')] # 查询重复行，重复值只在行内查找
df.drop_duplicates(keep='last')
df.loc[df.duplicated(subset=["python","java","php"])] # 查询python、java、php成绩相同的行
```

## 3、映射

replace属于fillna的高级版本

映射的含义：创建一个映射关系列表，把values元素和一个特定的标签或者字符串绑定

包含三种操作：

- replace()函数：替换元素（DataFrame\Series的函数)
- 最重要：map()函数：新建一列(Series的函数)
- rename()函数：替换索引(DataFrame的函数)

```python
df.replace(to_replace='tom', value='TOM') # 直接替换字符串
df.replace(to_replace=77, value=100) # 替换数字
df.replace(to_replace=["lucy","tom","jack"], value=["LUCY","MERY","JACK"]) # 使用列表替换
map_dic = {
    "lucy":"LUCY",
    "mery":"MERY",
    "tom":"TOM"
}
df.replace(to_replace=map_dic) # 使用字典替换
df["oldname"] = df.name # 新建一列
df.replace(to_replace={"name":"lucy"}, value="LUCY-1") # 使用字典处理某一列

# 正则替换
# to_replace 使用正则表达式
# regex 必须设置为True
df.replace(to_replace=r't.*', regex=True, value="ContainT") # t开头全部替换
```

### 3-1、a.replace()函数：替换元素

使用replace()函数，对values进行替换操作

### 3-2、Series替换操作

- 单值替换
  - 普通替换
  - 字典替换
- 多值替换
  - 列表替换
  - 字典替换（推荐）

Series参数说明：

- method：对指定的值使用相邻的值填充,method不能再DataFrame当中使用
- limit：设定填充次数

### 3-3、DataFrame替换操作

- 单值替换
  - 普通替换
  - 按列指定单值替换{列标签：替换值}

- 多值替换
  - 列表替换
  - 单字典替换（推荐）

**注意**：DataFrame中，无法使用method和limit参数

```PYTHON
df.drop("oldname", axis=1, inplace=True)
df.replace(to_replace=[22,77], value=[100,100])
df.replace(to_replace={22:110,77:110}) # 22换成77,77换成110
```

###  3-4、b.map()函数：新建一列

map 是Series的函数，所以通常被用来对某一列进行整体的映射处理

- map()可以使用字典映射新一列数据
- map()中可以使用lambda表达式
- map()中可以使用方法，可以是自定义的方法

**注意**

- map()中不能使用sum之类的函数，for循环
- map(字典) 字典的键要足以匹配所有的数据，否则出现NaN

```python
map_dic = {'lucy':"北京",'tom':"上海",'jack':"北京", 'tony':"上海", 'mery':"上海", 'rose':"北京"
} # 使用字典map
df["address"] = df.name.map(map_dic)
df['len_str'] = df['grammer'].map(lambda x: len(x))

def map_name(name):
    return map_dic.get(name,name)
df["address"] = df["name"].map(map_name) # 使用函数

# map的函数只能接收一个参数
def apply_age(x,bias):
    return x+bias
#以元组的方式传入额外的参数
data["age"] = data["age"].apply(apply_age,args=(-3,))

# 使用函数map
def score_5(score):
    if score > 90:
        return "A"
    elif score > 80:
        return "B"
    elif score > 70:
        return "C"
    elif score >= 60:
        return "D"
    else:
        return "E"
df.java = df.java.map(score_5)
df["php_5"] = df.php.map(score_5) # 函数映射
df.name = df.name.map(lambda x: x + '1班')  lambda 表达式映射
df.name.transform(lambda x: x + '学员') # transform()和map()类似

# 对一个DataFrame做遍历的时候，默认就是遍历它的列标签
# 完全可以把一个DataFrame当成字典来遍历
for column, v in score.items():
    print(column, v)
    print('---') # 结果是每次打印每列的值
```

### 3-5、c.rename()函数：替换索引，更改列名

仍然是新建一个字典

使用rename()函数替换行索引

- mapper 替换所有索引
- index 替换行索引
- columns 替换列索引
- level 指定多维索引的维度

```python
score.rename(columns={"name":"姓名"},inplace=True) # 改列名
score.rename(index={"张三":"tom", "李四":"jack"},inplace=True) # 改索引名
score.set_index("name", inplace=True) # 重新设置name为索引

mapper = {
    "张三":"tom",
    "李四":"jack",
    "语文":"文学",
    "英语":"外语",
    "上学期":"FIRSTCLASS",
    "下学期":"SECONDCLASS"
}
score.rename(mapper=mapper, axis=1) # 用字典替换，axis=1->改列名

total = pd.concat((score1, score2), axis=1, keys=["上学期","下学期"])
pd.concat([score1, score2，score3], axis=0,ignore_index=True) # 可以同时合并多个
total.rename(mapper=mapper, axis=1, level=-2)
score.columns = ["math","chinese","english"] # 也可以这样重命名列名
```
使用df.std()函数可以求得DataFrame对象每一列的标准差.

尊重业务的需求  5000  10000
异常值通用的界定办法：如果数据是呈标准正态分布的， |data| > 3\*|data.std()|
离群点的检测： 数值型的数据都可以使用离群点的方式来检测异常

```python
df = DataFrame(data=np.random.randn(1000,3), columns=list("ABC"))
# 任意一行至少存在一个数的绝对值大于该数所处列的3倍标准差，即认定为满足异常值条件
condition = (np.abs(df) > 3*df.std()).any(axis=1)
df.drop(df.loc[condition].index) # 删除异常列
```

根据每一列或行的标准差，对DataFrame元素进行过滤。

借助any()或all()函数, 测试是否有True，有一个或以上返回True，反之返回False

对每一列应用筛选条件,去除标准差太大的数据

删除特定索引df.drop(labels,inplace = True)

## 4、数据分类/组处理

```python
grouped_obj = heros.groupby("attack_range") # 根据attack_range分组，产生一个分组对象,这是对象
grouped_obj.groups # 查看分组对象的信息
grouped_obj.mean()[["hp_growth","hp_max"]] # 分组之后一定是聚合, 聚合运算只保留可运算的列

grouped_obj["hp_growth"].mean()
df.groupby('name').agg({'java':'mean','python':'max'}) # 分别对不同的列进行不同的聚合运算

df.groupby('name').mean()['java']
df.groupby('name')['java'].mean() # 效果同上

h1=heros[["name","hp_max","mp_max","attack_range","role_main"]].copy()
avg = DataFrame(h1.groupby("attack_range")["hp_max"].mean())

pd.merge(h1, avg, left_on="attack_range", right_index=True, suffixes=["", "_avg"])

grouped_obj1 = h1.groupby(["attack_range","role_main"])  # 多字段分组
grouped_obj1.groups

grouped_obj1.agg({"hp_max":"mean","mp_max":"mean"}).unstack(level=-2, fill_value=0) # 这其实是一个透视表
```

数据聚合是数据处理的最后一步，通常是要使每一个数组生成一个单一的数值。

数据分类处理：

- 分组：先把数据分为几组
- 用函数处理：为不同组的数据应用不同的函数以转换数据
- 合并：把不同组得到的结果合并起来

数据分类处理的核心：

 - groupby()函数
 - groups属性查看分组情况

- 根据item分组,查看结果

总结：数据类型是离散的可以分组，连续的没有意义

- 获取weight的总和

- 把总和跟df进行merge合并

- 使用列表进行多列分组，得到的结果是多层级索引

## 5、高级数据聚合apply()

### 5-1、apply直接在groupby后面，传入的是整个df，直接在df后传入的是一列或者一行

 使用groupby分组后，也可以使用transform和apply提供自定义函数实现更多的运算

- df.groupby('item')['price'].sum() <==> df.groupby('item')['price'].apply(sum)
- transform和apply都会进行运算，在transform或者apply中传入函数即可
- transform和apply也可以传入一个lambda表达式

```python
df['new']=df.apply(lambda x:x.php-x.java,axis=1)

h1.groupby("role_main")["mp_max"].mean()
h1.groupby("role_main")["mp_max","mp,min"].apply(np.mean) 
# 如果不是官方聚合函数，可以使用apply传递,按列计算时是每一列当成一个Series传入

# 分组对象的聚合函数，接受的是每一个分组,定制一个最大值和平均值的差的聚合函数
def group_function(x):
    return x.max() - x.mean()
df = DataFrame(h1.groupby("role_main")["mp_max"].apply(group_function))
df.columns = ["max-mean"]

df['yu_shu']=df.apply(lambda x:x['yu']-x['shu'])
```

**注意**

- transform 会自动匹配列索引返回值，不去重
- apply 会根据分组情况返回值，去重

### 5-2、交叉表

交叉表(cross-tabulation, 简称crosstab)是一种用于计算分组频率的特殊透视表。

```python
pd.crosstab(index=h1["role_main"], columns=h1["attack_range"])
```

### 5-3、透视表

透视表(pivot table)是各种电子表格程序和其他数据分析软件中一种常见的数据汇总工具。它根据一个或多个键对数据进行聚合，并根据行和列上得分组建将数据分配到各个矩形区域中。在Python和pandas中，可以通过本章所介绍的groupby功能以及（能够利用层次化索引的）DataFrame有一个pivot_table方法，此外还有一个顶级的pandas.pivot_table函数。除了能为groupby提供便利之外，pivot_table还可以添加分项小计（也叫margins）。

```python
# 透视表
pd.pivot_table(data=h1, values="hp_max", index="role_main", columns="attack_range",aggfunc="mean", fill_value=0)

pd.pivot_table(columns='behavior_type',index='hour',data=df,values='user_id',aggfunc=np.size) #透视图
df.groupby(['hour','behavior_type'])['user_id'].count().unstack(1).head() # 效果同上
```

## 6、分箱操作：cut和qcut函数

```python
rfm['M-SCORE']=pd.cut(rfm['M'],bins=[0,50,100,150,200,250,10000],labels=[1,2,3,4,5,6],right=False).astype(float)

df['categories'] = pd.cut(df['salary'],bins = [0,5000,20000,50000],labels=['低','中', '高'])
```

`x`：被切分的类数组（array-like）数据，必须是1维的（不能用DataFrame）；

`bins`：bins是被切割后的区间（或者叫“桶”、“箱”、“面元”），有3中形式：一个int型的标量、标量序列（数组）或者pandas.IntervalIndex 。一个int型的标量. 当bins为一个int型的标量时，代表将x平分成bins份。x的范围在每侧扩展0.1%，以包括x的最大值和最小值。标量序列. 标量序列定义了被分割后每一个bin的区间边缘，此时x没有扩 .pandas.IntervalIndex
定义要使用的精确区间。

`right`：bool型参数，默认为True，表示是否包含区间右部。比如如果bins=[1,2,3]，right=True，则区间为(1,2]，(2,3]；right=False，则区间为(1,2),(2,3)。

`labels`：给分割后的bins打标签，比如把年龄x分割成年龄段bins后，可以给年龄段打上诸如青年、中年的标签。labels的长度必须和划分后的区间长度相等，比如bins=[1,2,3]，划分后有2个区间(1,2]，(2,3]，则labels的长度必须为2。如果指定labels=False，则返回x中的数据在第几个bin中（从0开始）。

`retbins`：bool型的参数，表示是否将分割后的bins返回，当bins为一个int型的标量时比较有用，这样可以得到划分后的区间，默认为False。

`precision`：保留区间小数点的位数，默认为3.

`include_lowest`：bool型的参数，表示区间的左边是开还是闭的，默认为false，也就是不包含区间左部（闭）`duplicates`：是否允许重复区间。有两种选择：`raise`：不允许，`drop`：允许。

### 6-1、pd.qcut()参数介绍

基于分位数的离散化功能。 将变量离散化为基于等级或样本分位数的相等大小的存储桶。

和pd.cut()相比，pd.qcut()的参数少了两个，少了right和include_lowest两个参数，剩下的参数几乎和pd.cut()一模一样了。

```python
pd.qcut(x, q, labels=None, retbins=False, precision=3, duplicates='raise')
```

x ：一维数组或者Serise

q ： 表示分位数的整数或者数组，

- 如果是分位数的整数，例如10用于十分位，4用于四分位
- 如果是分位数数组，例如[0,0.25,0.5,0.75,1]用于四分位数

labels ： 数组或者布尔值，默认为none，用于指定每个箱体的标签

- 如果是数组，长度要与分箱个数一致，比如用四分位数分箱，需要指定四个标签
- 如果为False，则仅返回分箱的整数指示符，即当前数据位于哪个箱子中

rebines ：布尔值，可选。 是否显示分箱的分界值。（由于是按照分位数进行分箱，在不知道分位数具体数值的情况下，可以通过这个参数设置显示分界值即分位数的具体数值）

precision：整数，默认3，存储和显示分箱标签的精度。

duplicates：如果分箱临界值不唯一，则引发ValueError或丢弃非唯一

## 7、哑变量(独热编码)处理

```PYTHON
data = pd.DataFrame({"x":[1,2,3,4,5],"季节":["春","夏","秋","冬","秋"]})
data =pd.get_dummies(data,columns=["季节"],prefix_sep='_',dummy_na=False,drop_first=False)
data
```

- **data** : array-like, Series, or DataFrame 输入的数据
- **prefix** : string, get_dummies转换后，列名的前缀，默认为None
- **columns** : 指定需要实现类别转换的列名，否则转换所有**类别**性的列(数字列不会转换)
- **dummy_na** : bool, default False 增加一列表示空缺值，如果False就忽略空缺值
- **drop_first** : bool, default False 获得k中的k-1个类别值，去除第一个,防止出现多重共线性

**注意：**

若针对训练数据已经做好了独热编码并建立了模型，而**新增的预测数据或分类数据的类别变量未包含完整的类别**。此时直接使用pd.get_dummies，会发现与训练集得到的结果不一样。例如：训练数据中季节列中春、夏、秋、冬都出现过，而在新的测试数据中只出现了春与夏，两者的结果会不一样。

为了避免出现这个情况，需要新增以下代码：

```python
data['季节'] = data['季节'].astype(
    'category',
    categories=["春", "夏", "秋", "冬"]
)
```

## 8-时间序列

```python
from datetime import datetime, date, time ,timedelta # datetime模块里的datetime类,date类，time类
x=datetime(2020, 7, 3, 17, 6, 45)  # datetime.datetime(2020, 7, 3, 17, 6, 45)，生成一个日期时间对象，也可以只传入日期，时间默认为00:00:00
date(2020, 11, 15)  # datetime.date(2020, 11, 15)，创建一个日期
time(18, 23, 45) # datetime.time(10, 53, 21)，创建一个时间

x.date()  # 2020-07-03 # 提取日期对象
x.time()  # 17:06:45 # 提取时间对象
x.year  # 2020 # 整数int
x.month  # 7
x.day  # 3
x.hour  # 17
x.minute # 6
x.second # 45

# datetime转字符串，
x.strftime('%m/%d/%Y %H:%M:%S') # '07/03/2020 17:06:45'，指定格式
str(x) # '2020-07-03 17:06:45'，默认格式
# 字符串转datetime
datetime.strptime('7/6/2011 18:00:00', '%m/%d/%Y %H:%M:%S') #datetime.datetime(2011, 7, 6, 18, 0)
from dateutil.parser import parse # dateutil几乎可以解析所有日期表示形式
parse("Jan 31, 2020 10:45:02 PM") # datetime.datetime(2020, 1, 31, 22, 45, 2)

x.replace(minute=0,second=0) # datetime.datetime(2020, 7, 3, 17, 0)替换
datetime.now() # datetime.datetime(2020, 11, 15, 21, 31, 22, 838494) # 获取当前的日期时间
datetime.now() + timedelta(3)  # 计算三天以后的日期时间

# 时间差可以通过两个datetime对象相减得到，可以创建得到
dt=datetime(2011, 1, 7) - datetime(2008, 6, 24, 8, 15) # datetime.timedelta(days=926, seconds=56700),dt是timedelta对象
c = timedelta(days=9,weeks=2,hours=8,seconds=23,minutes=12) # datetime.timedelta(days=23, seconds=29543)，创建一个时间差
c.days # 取时间差的天数,有正负
c.seconds # 取时间差的秒数，为正

"""
在计算机中，时间实际上是用数字表示的。我们把1970年1月1日 00:00:00 UTC+00:00时区的时刻称为epoch time，记为0（1970年以前的时间timestamp为负数），当前时间就是相对于epoch time的秒数，称为timestamp。
"""

x.timestamp()  # 1593767205.0 # 获取对应的时间戳，可用时间戳求相隔小时
datetime.fromtimestamp(t) # 从时间戳获取时间

x.weekday() # 获取a所在的日期是周几，周一是0，以此类推

import pandas as pd
pd.to_datetime(....)
pd.date_range('1/1/2000', periods=3) # 生成连续的3天
pd.date_range('2012-04-01', '2012-06-01')
dfdf['付款日期'].dt # 转换成datetime
(datetime(2019,8,19,18,12,45)-dfdf['付款日期']).dt # 类似timedelta类型
```
