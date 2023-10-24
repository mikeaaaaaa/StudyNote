flak项目结构：

![1671341138473](flask框架学习.assets/1671341138473.png)

传统的web项目：

![1671341165982](flask框架学习.assets/1671341165982.png)

python项目：

![1671341207019](flask框架学习.assets/1671341207019.png)

+ model：主要和数据库打交道
+ template：主要包含html代码，是用户看到的部分
+ view：主要起控制作用，我们的python代码，基本写在这个模块

![1671342761875](flask框架学习.assets/1671342761875.png)

使用到的js、css文件都会放在static文件夹里面



WSGI：python web server gateway interface（可以理解为一个接口）

是为python语言定义的web服务器和web应用程序（或框架）之间 的接口

常见的 web框架有 flask、django、tornado等。

我们的目的就是使用flask框架构建一个 app，而这个app必须得部署在server上才能运行，才能被其他用户所访问。而flask与server之间的 接口就是 wsgi。为什么要有 wsgi？ 因为pyhton 框架太多了，让每一个框架都能和server 完美的融合。

而我们 创建的flask实例，实现了wsgi功能，所以他能自动把flaskapp与server融合。

![1671358524389](flask框架学习.assets/1671358524389.png)

那么问题来了，我们的 app.run()之后，哪来的服务器呢？ answer：flask内置了默认服务器，在前期我们可以使用自带的服务器。到后期 我们可以选择 自己搭建服务器，如nginx等。

当我们将flask app部署到server时，我们的浏览器就可以访问 该flask app。





![1671358664254](flask框架学习.assets/1671358664254.png)

名词理解： ==路由== 、==视图函数==  、==装饰器== 

具体实现步骤：

![1671361512907](flask框架学习.assets/1671361512907.png)

默认只能本机访问，如果想要别人能够访问你自己，那么你得 使用run方法的 ip参数将ip设置为 0.0.0.0 ,这样其他人才能访问你的app

run函数的 debug参数，为bool类型，默认为false，设置为ture时，可以将代码的更改即时反馈到服务器上，会自动发现代码的更改，自动  reload，在开发环境中要开启

==app.config :==可以看到以字典形式展示的 app配置！！！！  	

​	![1671367703238](flask框架学习.assets/1671367703238.png)

注意：这些所有的配置都是可以更改的

另外，常见环境有三种：

+ production
+ development (开发环境)
+ testing（测试环境）

至此我们有了最初的版本：

![1671369394194](flask框架学习.assets/1671369394194.png)

路由匹配原则：

+ 自上而下进行匹配，且只匹配一次
+ 视图函数不能重名

==用户浏览器访问流程== ：

![1671369677754](flask框架学习.assets/1671369677754.png)

+ 用户浏览器输入
+ 浏览器发送 request
+ 服务器 路由查询
+ 执行路由匹配的函数
+ 服务器返回response
+ 用户浏览器接收response

![1671369811761](flask框架学习.assets/1671369811761.png)

![1671369877522](flask框架学习.assets/1671369877522.png)

响应体交给浏览器，进行解释



@route函数讲解：

![1671370156361](flask框架学习.assets/1671370156361.png)

![1671370257425](flask框架学习.assets/1671370257425.png)

一个小插曲：《python中的闭包用法》：改用法出现在了 route![1671372136103](flask框架学习.assets/1671372136103.png)

![1671372184215](flask框架学习.assets/1671372184215.png)

route函数的源码：![1671372281277](flask框架学习.assets/1671372281277.png)

所以本质还是调用：add_url_rule()

![1671372481143](flask框架学习.assets/1671372481143.png)

变量规则：

![1671372688438](flask框架学习.assets/1671372688438.png)

注意：path类型也是字符串，与string不同的是它可以包含 /

uuid类型，是通过特殊的计算的出的一串值，是一种唯一标识，类型为UUID![1671373136874](flask框架学习.assets/1671373136874.png)

![1671373143740](flask框架学习.assets/1671373143740.png)

正常使用uuid时，一般会使用这两部，首先将其转为str类型，其次将横杠去掉：![1671373261778](flask框架学习.assets/1671373261778.png)![1671373303012](flask框架学习.assets/1671373303012.png)

==注意== ：视图函数只能返回

str、dict、response实例等

这几种类型

若返回的为dict，则自动封装的response的fcontenttype会自动变为 json：![1671373662251](flask框架学习.assets/1671373662251.png)

浏览器只能对response 的contenttype为 text的类型进行解释；

我们可以查看response的源码：！！！但我们一层一层向上溯源，就可以找到最顶层，发现其有如下属性：![1671373947259](flask框架学习.assets/1671373947259.png)，==这些属性我们都可以在创建的时候传入！！！！！！== 

==定制response== ：make_response()函数，可以返回response实例![1671374403930](flask框架学习.assets/1671374403930.png)

注意：

![1671373514212](flask框架学习.assets/1671373514212.png)

现在让我们了解一下 request对象：

Flask 模块里面有一个 request类，flask会自动帮我们生成request对象，我们在视图函数里面可以直接使用 resquest对象！！！![1671375011561](flask框架学习.assets/1671375011561.png)

![1671375187041](flask框架学习.assets/1671375187041.png)

（了解即可，用的很少）==看文档即可== 

render_template函数：![1671375416015](flask框架学习.assets/1671375416015.png),其实 render_template这个函数就是使用 ==jinjia2这个模板引擎== ，找到模板文件夹中的html文件，并转成字符串形式作为返回值，最后通过 视图函数的return 返回形成response





路由规则表： app.url_map



两种表单提交形式（通过修改form的method参数）：

（1）GET：适合方法：![1671421110461](flask框架学习.assets/1671421110461.png)

（2）PSOT：使用方法![1671421170926](flask框架学习.assets/1671421170926.png)

得到字典后 统一使用 get方法回去到 key对应的value值

register结合方法：

```python
@route('/register')
def register():
    if request.method == 'POST':
        password = request.form.get('password')
        name = request.form.get('name')
        return '注册成功'
    return render_template('register.html')
```



视图函数 的返回值 一般都为 render_template 或者 为 json字符串

redirect 方法：返回一个response，状态码为302，有location。![1671449197271](flask框架学习.assets/1671449197271.png)

==浏览器一看状态码 为302，之后立即根据location 重新向服务器再次请求==

url_for（）:路径反向解析函数。只需输入一个endpoint，自动就会发全名显示出来。很类似于dns解析，有时候我们在开发时，各种路径可能会非常难以记得，因此可以在定义时使用 endpoint参数，给 某个路由起一个小名称，在之后需redirect的时候使用 反向解析。![1671449793623](flask框架学习.assets/1671449793623.png)

![1671449780015](flask框架学习.assets/1671449780015.png)







总结：视图函数的返回值问题!![1671449908176](flask框架学习.assets/1671449908176.png)

其中str 可以是 json字符串！！！



jinjia模板介绍：==向html页面渲染数据==（jinjia2是一种模板引擎）：

![1671450508120](flask框架学习.assets/1671450508120.png)![1671450535774](flask框架学习.assets/1671450535774.png)



 ![1672289214516](flask框架学习.assets/1672289214516.png)

变量传入可以是 字符串、数字、列表、元组、列表， list 的访问还是用过 中括号访问，依然可以切片操作。

当然还可以传入自定义对象

jinjia模板还提供了一种极其方便的访问方式，比如 list.index  、 dict.key 、等可以直接使用点的方式访问元素

小结：![1672289725337](flask框架学习.assets/1672289725337.png)



控制块演示（for循环）：

![1672290031265](flask框架学习.assets/1672290031265.png)

循环块介绍：![1672291814127](flask框架学习.assets/1672291814127.png)

![1672305153521](flask框架学习.assets/1672305153521.png)

注意：因为有些在python中可以正常使用的函数在前端中可能使用不了，因此就需要使用到过滤器：

其实过滤器本质上就是模板引擎给我们做好的一个函数，我们只需要按照他的规则调用即可。![1672307869698](flask框架学习.assets/1672307869698.png)

很多内置的过滤器：![1672308131068](flask框架学习.assets/1672308131068.png)

列表操作：![1672308289181](flask框架学习.assets/1672308289181.png)

字典操作：![1672308311431](flask框架学习.assets/1672308311431.png)

两种自定义过滤器的方法：![1672308960884](flask框架学习.assets/1672308960884.png)

模板继承：

![1672381983550](flask框架学习.assets/1672381983550.png)

![1672382598093](flask框架学习.assets/1672382598093.png)

加载静态js、css文件，都是通过url_for方法来实现的。

![1672382428645](flask框架学习.assets/1672382428645.png)

体会 ==蓝图== 的用途：![1672389458893](flask框架学习.assets/1672389458893.png)

蓝图就是把之前的 路由 划分模块！！！