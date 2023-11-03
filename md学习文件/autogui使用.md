# pyautogui使用

==pyautogui一律简写为gui==

<https://zhangweixi.cc/static/windows-xy.html> 利用这个网站可以快速获取屏幕上某点的位置

![1666962014563](C:\Users\豪哥\Desktop\assets\1666962014563.png)

### 自动防止故障

```
pyautogui.FAILSAFE = False
```

默认为True，当鼠标在屏幕的最左上方时，程序会报错，防止程序无法停止

### 停顿功能

```
pyautogui.PAUSE = 1
```

设置完意味着所有puautogui的指令都会默认暂停一秒，防止操作过快

### 鼠标移动操作

#### 1.获取屏幕分辨率

```python
pyautogui.size()

width,height = pyautogui.size()

#1920,2080
```

#### 2.移动鼠标

##### 移动到指定位置

```python
pyguiauto.moveTo(100,300,duration = 1)
```

将鼠标移动到指定的坐标，坐标法则和我们之前学习的一样，左上方为原点，，duration的作用是设置时间，所有的gui函数都有这个参数

##### 按方向移动（相对移动）

```python
pyautogui.moveRel(100,500,duration)
```

表示向右移动100像素，向下移动500像素

#### 获取鼠标位置

```python
x,y = pyautogui.position()
```

### 鼠标控制点击

#### 单击鼠标

```python
pyautogui.click(x,y，button = 'left')
```

表示在屏幕的x，y位置单击,利用button可以选择是单机左键还是右键

left、right、middle三个选项

#### 双击鼠标

```python
pyautogui.doubleclick(x,y)#双击左键
pyautogui.rightclick(x,y)#双击右键
pyautogui.middleclick(x,y)#双击中键
```

#### 点击&释放

```python
pyautogui.moseDown() #鼠标按下
pyautogui.,mouseUp() #鼠标放下
```

#### 鼠标控制拖动（左键按下拖动）

```python
gui.dragTo(x,y,duration=time)
```

#### 鼠标拖动（相对拖动）

```python
gui.dragRel(100,500,duration=4)
#向右拖动100px，向下拖动500px，持续1秒
```

#### 控制鼠标滚动

```python
gui.scoll(300)
#向上滚动300个单位，单位根据系统的不同而不同
```

### 屏幕处理

#### 获取屏幕截图

gui 提供了一个方法，screenshot，可以返回一个Pillow的image对象，这里有三个常用的函数,==利用这个image对象可以更好地为我们判断下一步提供思路==

```python
im = gui.screenshow()
im.getpixel((x,y)) #返回这一点的像素的颜色，是一个rgb元组
gui.pixelMatchesColor(x,y,(rgb元组))#用来判断某一点的颜色与参数rgb是否相同

im.save("shot.png")#保存截图
```

#### 识别图像

​	首先，我们需要==先获得一个屏幕快照== ，例如我们想要点赞，我们就==先把大拇指的图片保存下来== ；然后使用函数：==locateOnScreen(‘zan.png’)==  ，如果可以找到图片，==则返回图片的位置== ，如：==Box(left=25, top=703, width=22, height=22)== ；如果找不到图片，则返回None;
如果，屏幕上有多处图片可以匹配，则需要使用==locateAllOnScreen(‘zan.png’)==  ，如果匹配到多个值，则返回一个==list== ，参考如下：

```python
import pyautogui
pyautogui.PAUSE = 1

# 图像识别（一个）
btm = pyautogui.locateOnScreen('zan.png')
print(btm)  # Box(left=1280, top=344, width=22, height=22)

# 图像识别（多个）
btm = pyautogui.locateAllOnScreen('zan.png')
print(list(btm))  # [Box(left=1280, top=344, width=22, height=22), Box(left=25, top=594, width=22, height=22)]

```

`pyautogui.center((left, top, width, height))` 

返回指定位置的中心点；这样，我们就可以再配合鼠标操作点击找到图片的中心 

### 键盘输入

#### **模拟按键按下**

**pyautogui.keyDown(按键名)**

#### **模拟按键释放**

pyautogui.keyUp() ： 

#### **模拟一次按键**；

pyautogui.press(按键名) ： # 就是调用keyDown() & keyUp()

#### 模拟键盘一次性输入多字符

pyautogui.typewrite('this',0.5) ： **第一参数是输入内容，第二个参数是每个字符间的间隔时间**；
pyautogui.typewrite(['T','h','i','s'])：**typewrite 还可以传入单字母的列表**；



#### 举例：

```python
pyautogui.keyDown('shift')    # 按下shift
pyautogui.press('4')    # 按下 4
pyautogui.keyUp('shift')   # 释放 shift

pyautogui.typewrite('$$$$', 0.5)

```

#### 键盘特殊按键

有时我们需要输入一些特殊的按键，比如向左的箭头，这些有相对应的键盘字符串表示，例如： 

```python
pyautogui.typewrite(['T','i','s','left','left','h',])   # 输出：This

```

解释：这里的left就是向左的箭头；诸如此类的键盘字符串，还有很多，参考下表： 

键盘字符串	说明
enter(或return 或 \n)	**回车**
esc	ESC键
shiftleft, shiftright	**左右SHIFT键**
altleft, altright	**左右ALT键**
ctrlleft, ctrlright	**左右CTRL键**
tab (\t)	**TAB键**
backspace, delete	**BACKSPACE 、DELETE键**
pageup, pagedown	**PAGE UP 和 PAGE DOWN键**
home, end	**HOME 和 END键**
up, down, left,right	**箭头键**
f1, f2, f3…. f12	**F1…….F12键**
volumemute, volumedown,volumeup	声音变大变小静音（有些键盘没有）
pause	PAUSE键，暂停键
capslock	CAPS LOCK 键
numlock	NUM LOCK 键
scrolllock	SCROLLLOCK 键
insert	**INSERT键**
printscreen	PRINT SCREEN键
winleft, winright	**Win键**（windows ）

#### 快捷键（组合键）

如果我们需要模拟复制的快捷键 ctrl + c ，如果用前面的方法，则代码为： 

```python
pyautogui.keyDown('ctrl')
pyautogui.keyDown('c')
pyautogui.keyUp('c')
pyautogui.keyUp('ctrl')
```

方法比较笨，就是逐个按下，再逐个放开

### 信息提示框（阻塞函数）

#### 警示框

```python
a = pyautogui.alert(text='This is an alert box.', title='Test')
print(a)
```

#### 选择框

```python
import pyautogui
a = pyautogui.confirm('选择一项', buttons=['A', 'B', 'C'])
print(a)
```

==输出如下图：点击B选项，返回值为‘B’==

#### 密码输入框

```python
import pyautogui

a = pyautogui.password('Enter password (text will be hidden)')
print(a)
```

输入密码，显示为密文，点击OK，返回值为刚刚输入的值； 



#### 普通输入

```python
import pyautogui

a = pyautogui.prompt('请输入一个数字：')
print(a)
```

显示为明文，点击OK，返回值为刚刚输入的值； 

## 实例：

### 鼠标控制 鼠标画一个正方形

```python
for i in range(2):   # 画正方形
    pyautogui.moveTo(200,200,duration=1)
    pyautogui.moveTo(200,400,duration=1)
    pyautogui.moveTo(400,400,duration=0.5)
    pyautogui.moveTo(400,200,duration=2)
	

```

### 获取鼠标的实时位置

```python

import pyautogui
import time

try:
    while True:
        x,y = pyautogui.position()
        posi = 'x:' + str(x).rjust(4) + ' y:' + str(y).rjust(4)
        print('\r',posi,end='')
        time.sleep(0.5)

except KeyboardInterrupt:
    print('已退出！')
```

### 获取鼠标位置 与 所在位置的颜色

```python
import pyautogui
import time

try:
    while True:
        x,y = pyautogui.position()
        rgb = pyautogui.screenshot().getpixel((x,y))
        posi = 'x:' + str(x).rjust(4) + ' y:' + str(y).rjust(4) + '  RGB:' + str(rgb)
        print('\r',posi,end='')
        time.sleep(0.5)

except KeyboardInterrupt:
    print('已退出！')
```

### 自动点赞程序

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# author：Zhang Kai 

import pyautogui
import time


def zan():
    time.sleep(0.5)    # 等待 0.5 秒
    left, top, width, height = pyautogui.locateOnScreen('zan.png')   # 寻找 点赞图片；
    center = pyautogui.center((left, top, width, height))    # 寻找 图片的中心
    pyautogui.click(center)    # 点击
    print('点赞成功！')


while True:
    if pyautogui.locateOnScreen('zan.png'):
        zan()   # 调用点赞函数
    else:
        pyautogui.scroll(-500)    # 本页没有图片后，滚动鼠标；
        print('没有找到目标，屏幕下滚~')

```

#### B站自动点赞

```python
def dainzan():
    time.sleep(0.5)  # 等待0.5s
    left, top, width, height = pyautogui.locateOnScreen('zan.png')  # 寻找图片
    center = pyautogui.center((left, top, width, height))  # 寻找图片中心位置
    pyautogui.click(center)  # 点击中心
    print("点赞成功")
 
 
while True:
    if pyautogui.locateOnScreen('zan.png'):
        dainzan()  # 调用点赞函数
    else:
        pyautogui.scroll(-500)  # 本页没有图片，滚动鼠标
        print("没有目标，滚动一下")
```

## python 函数总结

```python
import pyautogui
import pyperclip
pyautogui.PAUSE = 1 # 意味着所有pyautogui的指令都要暂停一秒

# 获得屏幕的分辨率
width, height = pyautogui.size()

 # 判断坐标是否在屏幕范围内
pyautogui.onScreen(100,200)


# 鼠标定位
pyautogui.position() # 当前鼠标位置


# 鼠标移动
pyautogui.moveTo(x=1098, y=748, duration=1, tween=pyautogui.easeInOutQuad) # 绝对坐标
pyautogui.moveRel(None, 50) # 相对坐标,以当前鼠标所在位置为基点  鼠标向下移动10像素
pyautogui.moveRel(-200, 50) # 向右200，向下50


# 鼠标点击
pyautogui.mouseDown()   #按下鼠标按键（左键）
pyautogui.mouseUp()     #释放鼠标按键（左键）
pyautogui.click()  #单击,一次鼠标点击包括按下和弹起
pyautogui.click(x=1098, y=748) # 默认鼠标左键 left
pyautogui.click(x=1098,y=748,button='left')
pyautogui.click(x=1098,y=748,button='right')
pyautogui.click(x=1098,y=748,button='middle')
pyautogui.rightClick()  #单击鼠标右键
pyautogui.middleClick() #单击鼠标中键
pyautogui.click(clicks=2) # 双击
pyautogui.doubleClick(x=1098,y=748,button="left") # 双击
pyautogui.click(button='right', clicks=3, interval=0.25) # 三击

# 鼠标移动后点击
pyautogui.click(x=1098,y=748,button='left')
pyautogui.click(x=1098,y=748,button='right'


# 鼠标拖拽（按下鼠标键并拖动鼠标）
# 鼠标拖曳，指从当前位置按下鼠标，移动至目标位置再释放的过程
pyautogui.dragTo(385,262,duration=1)  # 绝对，按下鼠标键不放，同时移动鼠标
pyautogui.dragRel(100,500,duration=4)  # 相对


# 鼠标滚动
pyautogui.click(x=387,y=746,button='left')
pyautogui.scroll(300)  # 向下滚动300个单位
pyautogui.hscroll(10) #  向右/左滚动 


# 键盘操作
pyautogui.keyDown('shift') #按键按下shift
pyautogui.keyDown('alt')
pyautogui.keyUp('alt')  # 按键释放

pyautogui.press('esc') # 就是调用keyDown() & keyUp(),模拟一次按键
pyautogui.press(['left', 'left', 'left', 'left', 'left', 'left']) #连续按键left


# 按下键盘组合键
pyautogui.hotkey('win', 'r')
pyautogui.hotkey('ctrl', 'v')   # 组合键
pyautogui.hotkey('alt', 'tab')   # 组合键
pyautogui.hotkey('ctrl', 'shift', 'esc') #调出任务管理器
 

# 输入文本
pyautogui.typewrite('Hello world!', interval=0.1) #模拟输入
pyautogui.typewrite(['a','b','left','left','X','Y']) #可以传入单字母的列表
pyautogui.typewrite(['T','i','s','left','left','h',])   # 键盘特殊顺序输出,输出：This

# 截全屏
im1 = pyautogui.screenshot()
im1.save('11.png')
im2 = pyautogui.screenshot('11.png')
im = pyautogui.screenshot(r'12.png',region=(0, 0, 300, 400))# 截取部分屏幕


# 消息窗口
...

```

## 微信信息轰炸

https://tanxg.blog.csdn.net/article/details/121371490

案例来自该网站

```python
import pyautogui
import pyperclip  #在本案例中的作用是自己。。。
# pip  install  -i  https://pypi.doubanio.com/simple/  --trusted-host pypi.doubanio.com  pyperclip

# 用截图软件确定微信的位置
# duration类似于移动时间或移动速度，省略后则是瞬间移动到指定的位置
pyautogui.moveTo(x=1098, y=748, duration=1, tween=pyautogui.easeInOutQuad)
pyautogui.click()  #单击
# 找到输入框（这里已经确定好要发的对象）
pyautogui.moveTo(x=656, y=552, duration=0.5, tween=pyautogui.easeInOutQuad)
pyautogui.click()  #单击
# 循环10次
for i in range(10):
    pyperclip.copy('信息轰炸，不要管我！')
    pyautogui.hotkey('ctrl', 'v')   # 组合键
    pyautogui.press('enter') 

```

![1666962482853](C:\Users\豪哥\Desktop\assets\1666962482853.png)

```python
import pyautogui
import pyperclip
pyautogui.click(x=1098, y=748,duration=1,button='left')
pyautogui.click(x=656, y=552,duration=1,button='left')
for i in range(10):
    pyperclip.copy('信息轰炸，不要管我！')
    pyautogui.hotkey('ctrl', 'v')   # 组合键
    pyautogui.press('enter') 

```

## 操作浏览器

```python
import pyautogui
import pyperclip
# pip  install  -i  https://pypi.doubanio.com/simple/  --trusted-host pypi.doubanio.com  pyperclip
import time
pyautogui.click(x=340, y=753,duration=1,button='left') # 点击浏览器
pyautogui.click(x=101, y=83,duration=1,button='left') # 点击收藏栏中的百度图标
time.sleep(3) # 要缓一下
pyautogui.click(x=394, y=312,duration=1,button='left') # 在百度输入框，输入内容
pyperclip.copy('卖山楂啦prss')
pyautogui.hotkey('ctrl', 'v')   # 组合键，输入内容
pyautogui.press('enter') 
pyautogui.click(x=400, y=248,duration=1,button='left') # 点击第一个搜索结果

```

## 自动填写表单

```python
import time
import pyautogui as pag
import webbrowser

webbrowser.open_new("https://formsmarts.com/html-form-example")
#Let the web browser finish loading before clicking (sleep for 2 seconds)
time.sleep(3)

pyautogui.moveTo(x=1357, y=207, duration=1, tween=pyautogui.easeInOutQuad)
pag.dragTo(1356,431,duration=1)  # 绝对，按下鼠标键不放，同时移动鼠标

pyautogui.click(x=419, y=265,duration=1,button='left') # First Name 
pyperclip.copy('Tan')
pyautogui.hotkey('ctrl', 'v')   # 组合键，输入内容

pyautogui.click(x=435, y=316,duration=1,button='left') # Last Name
pyperclip.copy('GGG')
pyautogui.hotkey('ctrl', 'v')   # 组合键，输入内容

pyautogui.click(x=456, y=365,duration=1,button='left') # 邮箱
pyperclip.copy('123@qq.com')
pyautogui.hotkey('ctrl', 'v')   # 组合键，输入内容

pyautogui.click(x=438, y=411,duration=1,button='left') # Subject of Your Inquiry
pyautogui.click(x=446, y=461,duration=1,button='left') # Subject of Your Inquiry

pyautogui.click(x=436, y=459,duration=1,button='left') # Inquiry 
pyperclip.copy('今天你真好看！')
pyautogui.hotkey('ctrl', 'v')   # 组合键，输入内容


pyautogui.click(x=418, y=649,duration=1,button='left') # 点击继续

```

