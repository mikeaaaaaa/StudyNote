# python-日志管理

## logging日志级别

![1678713910112](logging.assets/1678713910112.png)

默认只输出 warning以及以上的日志级别，但也可以手动指定输出的日志级别

![1678714355390](logging.assets/1678714355390.png)

生成日志，并输出到文件中，可以设置 写入的模式为‘w’、‘a’

![1678714962802](logging.assets/1678714962802.png)

三种写入的方法啊：（三种字符串化的方法）![1678715132814](logging.assets/1678715132814.png)



输出格式和添加一些公共信息：

![1678715272303](logging.assets/1678715272303.png)

调整时间格式：![1678715381530](logging.assets/1678715381530.png)

## logging高级功能 

![1678716621567](logging.assets/1678716621567.png)

![1678717252536](logging.assets/1678717252536.png)

logging 实例相当一一支笔，负责记录

![1678717316515](logging.assets/1678717316515.png)



![1678806641582](logging.assets/1678806641582.png)

常见的几个handlers

![1678806671332](logging.assets/1678806671332.png)

![1678806708227](logging.assets/1678806708227.png)

![1678806825385](logging.assets/1678806825385.png)

```python
# 用编程的方式，（更加高级的写法）

```



![1678807172418](logging.assets/1678807172418.png)

applog仅仅是记录器的名字，注意，如果想要让 handler中设置的级别起作用，那么我们得在创建 logger时设置，logger.setlevel(logging.DEBUG)即最低级别

![1678807201693](logging.assets/1678807201693.png)

在设置formatter时可以在s的前面写一个数字，以让对齐！！！，就是字符串对齐的知识

![1678807238651](logging.assets/1678807238651.png)

![1678807310223](logging.assets/1678807310223.png)

结果（console）：

![1678807337323](logging.assets/1678807337323.png)

结果（file）：

![1678807660221](logging.assets/1678807660221.png)

## 以配置文件方式

![1678808049820](logging.assets/1678808049820.png)

