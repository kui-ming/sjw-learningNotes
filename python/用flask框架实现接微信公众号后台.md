# 用flask框架搭建微信公众号的后台

> 最近用python写了点爬虫，为了要让爬取的数据能够随时显示在我眼前，并实时根据我的指令返回数据。于是采用微信公众号做这个显示窗口，既能发送指令也能显示简单的相关数据。



##### 准备工具

- python3.x环境

- pycharm
- 一台服务器（可以使用内网穿透）
- 申请一个微信公众号



#### 一、搭建微信公众号后台



##### 查看公众号

​		登录公众号平台，在基础配置中设置相关信息，点提交时微信会访问我设置的接口，如果我返回了正确的信息，那我的公众号就与我的这个接口绑定上了，之后微信所有的消息都将通过调用我的这个接口来与我的服务器交互。现在提交肯定是失败的，我还没有配置后端，自然是请求不到，且看我下面的操作。

![image-20221106112648079](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211061335151.png)



##### 使用flask框架搭建一个后端服务器

​		创建`app.py`，然后通过flask搭建后端服务器，这里的代码是完整代码，其中WxHandle是响应微信请求的具体实现。这个项目一直监听的是8800端口，但我后面会用nginx反向代理一波。

**app.py**

```python
from flask import Flask, request
from loguru import logger

from wx_handler import WxHandle

# 配置web框架
app = Flask(__name__)
# 日志文件保存10天日志，最大存储500M
logger.add("./log/runtime_{time}.log", retention="10 days", rotation="500 MB")


# 暴露路由，接收get和post请求
@app.route('/', methods=["GET", "POST"])
def wx_listener():
    # 通过getattr获取到WxHandle的静态get或post方法，lower是为了将大写method值转为小写，与WxHandle中的方法名对应
    fun = getattr(WxHandle, request.method.lower())
    # 调用得到的get或post方法
    return fun()


if __name__ == "__main__":
    # 监听8800端口
    app.run(host="0.0.0.0", port=8880)


```



##### WxHandle的具体实现

​		WxHandle是响应微信消息的具体入口，所有的请求都或通过post或get方法来得到回复。这其中的签名算法和具体消息对象也会在下面体现。

**wx_handle.py**

``` python
from flask import request
from loguru import logger

from wx.verification import signature as f_signature	# 签名算法
import wx.receive as receive	# 接收微信消息的地形
import wx.reply as reply		# 将要答复的信息包装成微信需要的xml格式


class WxHandle:

    @staticmethod
    def post():
        """
        响应微信的post请求，微信用户发送的信息会使用Post请求
        :return:
        """
        try:
            logger.info("接收微信消息->\n"+str(request.data))
            # 对微信传来的xml信息进行解析，解析成我们自定义的对象信息
            receive_msg = receive.parse_xml(request.data)
            # 如果解析成功
            if isinstance(receive_msg, receive.Msg):
                # 该微信信息为文本信息
                if receive_msg.type == "text":
                    # 创建一条文本信息准备返回给微信，文本内容为“测试”
                    msg = reply.TextMsg(receive_msg, "测试")
                    # 发送我创建的文本信息
                    return msg.send()
                else:
                    # 该信息不为文本信息时，发送我定义好的一条文本信息给他
                    return reply.Msg(receive_msg).send()
        except Exception:
            logger.error("解析微信XML数据失败！")
        return "xml解析出错"

    @staticmethod
    def get():
        """
        响应微信的get请求，微信的验证信息会使用get请求
        这里的验证方式是按照微信公众号文档上的教程来做的
        :return: 
        """
        # 微信传来的签名，需要和我生成的签名进行比对
        signature = request.args.get('signature')   # 微信已经加密好的签名，供我比对用
        timestamp = request.args.get('timestamp')   # 这是我需要的加密信息
        nonce = request.args.get('nonce')           # 也是需要的加密信息
        # 判断该请求是否正常，签名是否匹配
        try:
            # 微信传来的签名与我加密的签名进行比对，成功则返回指定数据给微信
            if signature == f_signature(timestamp, nonce):
                # 微信要求比对成功后返回他传来的echost数据给他
                return request.args.get('echostr')
            else:
                return ""
        except Exception:
            logger.error("签名失败！")
        return "签名失败！"


```



##### signature签名算法的具体实现

​		这是根据微信要求编写的签名算法，目的是于微信传来的签名进行比对，以此来保证请求的正确性。

**wx/verification.py**

```python
import hashlib
TOKEN = "kuiming"   # 这个token要与我在微信公众号上设置的token是一样的


def signature(timestamp, nonce):
    """
    根据微信公众号文档写的微信需要的签名算法
    :param timestamp:
    :param nonce:
    :return:
    """
    # 接收微信服务器传来的时间戳和随机值，与我们自己设定的Token值进行排序后组成一个字符串
    signature_list = [TOKEN, timestamp, nonce]
    # 对列表进行排序
    signature_list.sort()
    # 组成字符串
    ciphertext = "".join(signature_list)
    # 进行sha1算法加密
    sha1 = hashlib.sha1()
    # python3.x后的算法写法
    sha1.update(ciphertext.encode("utf-8"))
    # 返回加密后的签名
    return sha1.hexdigest()

```



##### 消息接收类与消息响应类

​		为了降低模块间的耦合性，我将消息类拆分为接收消息类和响应消息类。接收消息类会接收微信发来的消息，并将它解析为对象，这样可以方便我们之后的操作。回复消息类是将我们想要回复给用户的消息又打包为一个xml格式的微信消息包，然后再通过WxHandle中的接口信息返回回去，形成一个有效交互。

**wx/receive.py**

```python
from lxml import etree	# 用来解析xml格式的数据的库


def parse_xml(web_data):
    """
    解析微信传递来的消息，根据消息类型转换为不同的对象
    :param web_data:
    :return:
    """
    # 解析xml数据
    xml = etree.XML(web_data)
    # 查看消息类型
    msg_type = xml.find('MsgType').text
    if msg_type == 'text':
        # 为文本时生成文本对象
        return TextMsg(xml)
    elif msg_type == 'image':
        # 为图像是生成图像对象
        return ImageMsg(xml)
    return None


class Msg:
    """
    定义消息的基本格式，是一些类型消息的父类，解析XML格式的微信信息
    """
    def __init__(self, xml):
        self.toUser = xml.find('ToUserName').text   # 公众号的微信号
        self.fromUser = xml.find('FromUserName').text   # 发送消息的用户的openid
        self.time = xml.find('CreateTime').text     # 创建时间
        self.type = xml.find('MsgType').text        # 消息类型
        self.id = xml.find('MsgId').text            # 该消息的id，每天消息都有独立的id


class TextMsg(Msg):
    """
    解析文字类信息
    """
    def __init__(self, xml):
        Msg.__init__(self, xml)     # 为父类的属性赋值
        self.content = xml.find('Content').text.encode("utf-8")     # 传递来的信息需要经过utf-8编码


class ImageMsg(Msg):
    """
    解析图片信息
    """
    def __init__(self, xml):
        Msg.__init__(self, xml)
        self.picUrl = xml.find('PicUrl').text
        self.mediaId = xml.find('MediaId').text

```



**wx/reply.py**

​		这里要非常**注意**，回复消息的发送者是我们自己，而接收消息者是用户，所以必须把我们接受到的微信消息的接收者和发送者调换一下才能正确回复。

```python
import time
import wx.receive as receive


class Msg:
    def __init__(self, receive_msg: receive.Msg):
        """
        将回复用户的信息按照微信的xml格式进行包装
        :param receive_msg: 
        """
        self.dict = dict()
        # 这里是我发送信息，所以发送给我们收到的微信消息的发送者
        self.dict['ToUserName'] = receive_msg.fromUser
        # 而是谁发送的呢？自然是我们收到的微信消息的接收者，也就是我的公众号
        self.dict['FromUserName'] = receive_msg.toUser
        # 发送时间
        self.dict['CreateTime'] = int(time.time())
        # 发送的信息文本，这里是默认的文本
        self.dict['Content'] = "对不起，我没有看懂你的信息~"
        pass

    def send(self):
        # 发送的xml格式
        xml = """
                    <xml>
                        <ToUserName><![CDATA[{ToUserName}]]></ToUserName>
                        <FromUserName><![CDATA[{FromUserName}]]></FromUserName>
                        <CreateTime>{CreateTime}</CreateTime>
                        <MsgType><![CDATA[text]]></MsgType>
                        <Content><![CDATA[{Content}]]></Content>
                    </xml>
              """
        # 将当前对象的dict属性填入到xml文本中，对应的{ToUserName}、{FromUserName}等
        return xml.format(**self.dict)


class TextMsg(Msg):
    def __init__(self, receive_msg: receive.Msg, content):
        super().__init__(receive_msg)
        self.dict['Content'] = content

    def send(self):
        xml = """
            <xml>
                <ToUserName><![CDATA[{ToUserName}]]></ToUserName>
                <FromUserName><![CDATA[{FromUserName}]]></FromUserName>
                <CreateTime>{CreateTime}</CreateTime>
                <MsgType><![CDATA[text]]></MsgType>
                <Content><![CDATA[{Content}]]></Content>
            </xml>
            """
        return xml.format(**self.dict)


class ImageMsg(Msg):
    def __init__(self, receive_msg: receive.Msg, media_id):
        super().__init__(receive_msg)
        self.dict['MediaId'] = media_id

    def send(self):
        xml = """
            <xml>
                <ToUserName><![CDATA[{ToUserName}]]></ToUserName>
                <FromUserName><![CDATA[{FromUserName}]]></FromUserName>
                <CreateTime>{CreateTime}</CreateTime>
                <MsgType><![CDATA[image]]></MsgType>
                <Image>
                <MediaId><![CDATA[{MediaId}]]></MediaId>
                </Image>
            </xml>
            """
        return xml.format(**self.dict)

```



#### 二、部署到服务器

##### 准备

​		整个部署操作我不会使用shell命令，而是通过部署在服务器中的宝塔面板自动完成。

##### 将项目移动到服务器

​		这里通过宝塔面板直接把项目拖到了指定目录里

![image-20221106125950927](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211061335152.png)



##### 使用宝塔应用管理器启动应用

​	在宝塔面板的软件商店里下载宝塔应用管理器，用来管理和运行项目

![image-20221106130327514](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211061335153.png)

![image-20221106130445579](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211061335155.png)



##### 配置nginx反向代理

​		当项目启动后监听的是8800端口，并且还是http协议。而我配置nginx会把所有http协议的请求转为https协议，这会导致找不到这个接口。于是为了接口美观和正常使用，我将其代理到433端口下的/wx_zhuxuebao路由下，这也是为什么我第一张图中配置的URL中设置的是/wx_zhuxuebao了。

![image-20221106131000117](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211061335156.png)



##### 完成

​		到这里最基础的项目就部署好了，下一步准备在微信中进行测试。



#### 三、测试

成功实现了我期待的效果，其中返回**”测试“**是因为在`WxHandle`类的post方法中判断如果是文字则

``` python
# 创建一条文本信息准备返回给微信，文本内容为“测试”
msg = reply.TextMsg(receive_msg, "测试")
# 发送我创建的文本信息
return msg.send()
```

返回**”对不起，我没有看懂你的信息~“**是因为在`WxHandle`类的post方法中判断如果不是文字则

```python
else:
   # 该信息不为文本信息时，发送我定义好的一条文本信息给他
   return reply.Msg(receive_msg).send()
```

而`Msg`类中存在Content属性

```python
# 发送的信息文本，这里是默认的文本
self.dict['Content'] = "对不起，我没有看懂你的信息~"
```



##### 测试截图

![image-20221106131641934](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211061335157.png) 

