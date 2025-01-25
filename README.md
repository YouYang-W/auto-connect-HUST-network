## 前言

仿照Kingdo777的思路与代码，实现HUST校园网自动连接。

已有问题：实验室校园网会不定时断开连接, 需要手动点击认证, 
解决方案：通过py脚本实现自动认证，防止校园网断开连接无法远程。


## 源码解析

主函数:
```python
if __name__ == '__main__':
    while True:
        if has_network():
            print("Y")
            time.sleep(10)
        else:
            print("N")
            login()
            time.sleep(10)
```
每个10秒检查是否连接到网络, 如不可，

调用`login()`函数来自动重连



## 校园网认证流程
进入这个认证的页面, 按F12进入开发者模式

![](img/img.png)

我们会在Source中看到两个重要的JS文件,当我们点击网页中的`连接 Login`的时候就会调用这个函数.

![img_1.png](img/img_1.png)

这个函数会收集你的一系列信息,并最终调用:`AuthInterFace.login`

![img_2.png](img/img_2.png)

`AuthInterFace`定义在 `AuthInterFace.js`中.

其中`init()`用于生成请求的url, `login()`首先将传入的参数合并成`content`,最后通过post方法发送请求

![img_3.png](img/img_3.png)

因此需要我们做的就是, 获取`content`的内容, 然后我们就可以用python对指定的url发起post请求

获取content的方法也很简单, 只需要在如图位置打上断点, 然后点击网页中连接的按钮,当js执行到断点后,就可以复制content的值

![img_4.png](img/img_4.png)


## 使用

0. 在终端输入 `git clone https://github.com/YouYang-W/auto-connect-HUST-network.git`


1. 将获取到的`content`信息存放到`main.py`的同目录下的名为`content`的文件中。注意点击win文件管理器的查看，选择展示文件拓展名，`content`文件不要有任何后缀。`content`内容无需加入引号。


2. 执行python脚本


3. 不过windows下需要安装npcap, 可以点击以下链接下载安装
   
   https://nmap.org/npcap/dist/npcap-1.60.exe

## 后记

1. 如果切换了Wifi, 导致IP地址改变, 那么需要重新获取content的值. 
