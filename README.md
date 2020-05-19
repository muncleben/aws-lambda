# AWS Lambda service的学习笔记



## AWS Lambda专题一：初识AWS Lambda

什么是AWS Lambda？

AWS官方是这么介绍滴：AWS Lambda is a compute service that lets you run code without provisioning or managing servers. AWS Lambda executes your code only when needed and scales automatically, from a few requests per day to thousands per second.

So AWS Lambda是AWS的一个云服务，而且是一个serverless的服务，你只管写代码实现你想要的功能就好，其它的你不需要管，都交给AWS来负责了。AWS Lambda只会在有需要的时候才去运行你的代码，并且根据请求规模大小，自动调整执行级别，从每天几个请求到每秒几千个请求。

其实说白了，AWS Lambda就是一个可以执行代码或者脚本的一个平台。大家可以回想一下，如果你想用Python语言打印一句：Hello World!，就这么一句话，你需要什么？

一台电脑 （硬件）+ 操作系统 + Python软件 + 编辑器（Python自带的也行或者其它的IDE工具），差不多就这么多吧。

那现在如果你用AWS Lambda服务的话，直接创建一个AWS Lambda服务，选择Python作为运行时环境，然后开始写你的代码就好了。除了你的代码，其它的像Python这个软件和运行代码时需要用多少CPU啊，需要多少内存啊。。。这些你通通不用管，如果你的代码跑的时候，需要更多的计算资源，AWS Lambda这个服务的底层会自动申请更多的资源来做支持。

目前AWS Lambda支持的编程语言有：

- .NET Core 3.1(C#/PowerShell)
- Go 1.x
- Java 11
- Node.js 12.x
- Python 3.8
- Ruby 2.7

下面的这些老版本也支持：

- .NET Core 2.1(C#/PowerShell)
- Java 8
- Node.js 10.x
- Python 3.6
- Python 3.7
- Python 2.7
- Ruby 2.5

另外还可以添加其它的编程语言，目前还没试过添加其它编程语言，有兴趣的话大家可以自己试试。



**AWS Lambda能做什么呢？**



大佬们都总结好了，我帮大家抄过来了：

**Use Cases of AW****S Lam****bda**

AWS Lambda used for a wide range of applications like:

- Helps you for ETL process
- Allows you to perform real-time file processing and real-time stream processing
- Use for creating web applications
- Use in Amazon products like Alexa Chatbots and Amazon Echo/Alexa
- Data processing (real-time streaming analytics)
- Automated Backups of everyday tasks
- Scalable back ends (mobile apps, loT devices)
- Helps you to execute server-side backend logic
- Allows you to filter and Transform data

Source: https://www.guru99.com/aws-lambda-function.html



目前个人理解：AWS提供的所有服务，只要有事件功能的，基本上都可以通过AWS Lambda来自动化一些操作。由于本人目前只了解AWS S3服务，所以接下来的几篇文章，我都是基于AWS S3服务来学习AWS Lambda的相关知识，而且我们选择Python来编写相应的代码。

那接下来，我们就来创建一个AWS Lambda，来好好认识一下它。



登录到你的AWS之后，我们在控制台搜索关键字：lambda，Lambda这个服务会自动被检索出来，选择即可，然后页面会自动跳转到AWS Lambda服务页面。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6jdXAOvrOecc8FAZKAxXX6NMEu7OomO8l9eaxS6PoN5FhicBiank1JKPJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 



 在AWS Lambda服务页面，点击：Create Function  ![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6j2dP075YJoVe8GQGIm1v5IEg9gbvKKiaAhrn9QxKK0Bt1W65lRL0icYPw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 



在Create function第一个选项，保持默认"Author from scratch"选项。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6jDMmoLjpXvhMyjIDE4MmGsVCmkBl2JzYOXQLEH7lX20QUsZwKLEibnDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



接下来在Basic information部分，你需要在Function name文本框里给你的function起一个名字，养成见名知意的好习惯。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6jdn56PJjGrCejJLB1dtEOxibr9hQwNgEdKxCng71yaibKXce3IIQHN5Wg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Permissions这里先暂时保持默认，因为我们这一节只是写一个最简单的AWS Lambda的function：用Python打印一句Hello, AWS Lambda! 最后点击Create function按钮。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6j7CZQGltibNOSPoLnnsfUULOr9FU8A8YEwhwgAhVTV1VVRO0mn7xVwGg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



function创建好之后，页面会自动跳转到创建成功页面，如下图所示。这个界面布局，熟悉一下，没事儿多点点。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6jDagmjuLyLLEB7eW8SVKicM7VCQSNdKb5QoIbBptb9FPlVnVHmKJwAlw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



往下翻页面，你会看到Function code区域，写过代码的同学，是不是很熟悉？其实就是一个在线的代码编辑器，Runtime选项那里支持多种语言，由于我们创建function的时候，已经指定了Python，所以Runtime那里是Python 3.8，代码区域默认生成的也是Python代码。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6jHZficxkf4V5gibL5G39OAOcHU806xkkxuYZgfoxJu7yJkNfhDicEh5LSg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



最上面一行功能导航栏，常用的就是 Test 和 Save，其实在下面的代码编辑器部分，也有一个功能导航栏，也有 Save 和 Test。详见下图。



我们现在将代码改成下面的样子，只输出一句话：Hello, AWS Lambda! 然后点击 Save 按钮。

```python
import jasondef lambda_handler(event, context):
    #TODO implement
    info = "Hello, AWS Lambda!"
    return info
```



![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6jiaiclOckACf4uLSSbf72z6TlR6q0JKicEKam59kEL6pWkPlNMpmyicTw0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



点击Save按钮之后，Configure test event页面会跳出来，你只需要填写一下Event name就可以，名字还是要见名知意，下面的字典内容不用管。

然后点击右下角的Create按钮。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6j5aLlUl87wEicrP3AOzjQb34VptpB7uKKVuibFKTofb6hVWUsfbpFelng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



好了，现在我们就可以测试我们的代码了，点击Test按钮，运行结果会显示在代码区域下面的Execution results区域。看到了吧？我们成功打印了一句：Hello, AWS Lambda!

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oMRcNgraqQ0bmPia6uvjn6j5a15HJpzuhMNC8BpSu37Y0wJ1NTBGd3Eklhlfx23YVVXer5GgStKAA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



好了，本节内容就先介绍到这里，下一节我们再多加一个功能：将AWS S3与AWS Lambda做关联，当有新文件上传到AWS S3的bucket里时，AWS Lambda将自动被触发并且执行。





