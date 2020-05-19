## AWS Lambda专题二：AWS S3触发Lambda function

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXD8bicwWLrVC9nW2UW7g0jVQXyYiaqj5XT52wiaP34ibeBAtMUKI1QDrummA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



本篇目标：实现如何利用AWS S3的Event属性来触发Lambda function执行。



在上一篇专题中，我们介绍了如何创建一个基于Python运行时的AWS Lambda function，并执行了它。那么本篇专题，我们就来用AWS S3服务来触发AWS Lambda function，让我们在上一篇专题中创建的AWS Lambda function自动执行。



那如何让AWS S3这个服务去触发AWS Lambda服务呢？想必你已经想到了，上一个专题我们提到过一个关键词：事件。这个是最重要的一个概念，很多互联网技术都会有event这个概念。当我们在AWS S3上开启了event功能，就可以和AWS Lambda做关联，去触发lambda function了。大概的流程是这么个样子：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDj9UwfxVmaHDDNUNxA97ZhGchcFibrvfS2H2AOE9DToxAQsA9wQmJuQA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



那我们现在就开始动手开搞。



登录AWS，在Service里直接输入s3，然后选择"S3 scalable storage in the Cloud"

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDrEMCXZbyG3pJqXesosbgtULOXibAanwCopHryW1UsKBtOV5gg5sW66Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



页面将会直接跳转到S3服务页面，这里说明一下Bucket，网上一般翻译成存储桶，还是比较贴切这个服务的，Bucket也是S3服务的一个最基本也是最重要的一个概念。记住bucket就是存放你数据的地方就行了，你也可以把一个bucket就是Windows下的一个磁盘。那我们现在就来创建一个bucket。



点击"Create bucket"按钮。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDnhTUtRiaSCwFCOMRrchMQNd4dzFBHo0hp5wlibcobJWYvibRrDOqpdzicQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在Create bucket页面，需要注意的地方有：

\- Bucket name: 必须要提供的，而且是要唯一的，不能有空格或者大写字母。

\- Region：必选项，必选指定一下你想把你的bucket放在AWS的哪个数据中心。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXD5VME1J7sgtf9yGlUg5zZ7yqy4ocCCIkuHo6Pibv0VDicYoAmfqnHwnMA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在Bucket settings for Block Public Access这一部分，我们保持默认即可。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXD9t5MibH1ziaia1M2x2uSTMt14rGtmqWQ8Tf5jJSK1fBnXcNxEVCB9JFXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



然后点击："Create bucket"按钮。Bucket创建成功的速度取决于你本机电脑的网速。这里吐槽一下中国移动的网速，真TMD慢啊！



Bucket创建成功会有如下提示：

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDzILVvSEryVorPC938DZCGZa1muHNQI1w2fwCN3LGlHVqwKJn8STTsA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们可以看到，刚才的bucket的详细信息和创建时间

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDfOChYrhhw5e2UFNfJe0gac4kaAHBibgGfB0pMIgJ1lOVNlBQ18ZfN6w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



然后点击我们新创建的bucket的名称：demo.lambda.bucket，我们会进入到这个bucket里面。接着点击"Properties"这个tab。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDVs5X1vIEs5a6Uoegrn8kenAULgxnYP2gH1yZRRLJxAuUWibe5KuFEWQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在Advanced settings部分，有一个Events属性，这个就是我们要用的属性，然后点击 Events

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDRsgicBmZuMSkx8vCqDDGSpNDbVlCQqqLQyNDqZEO4vM0ptlk9FWY6BQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在新显示的页面，点击："Add notification"

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDM6Ak4xFA1DGdlDz1pKsTvPC07BNCvd5hUeFHe5v9zJJiaWyrVrgeqrA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



然后我们：

\- 给这个新建的event起个名字

\- Events type：我们来选择PUT，因为我们这次只是测试文件上传操作。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDX6aRelJP1l5aKVnKGX1zPUeLnRMqpo4NZQEUkQb7Rsa2fv5icv4GGJQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们可以指定前缀和后缀，从而来实现对批量类似文件类型的操作，这里我们不指定。



下面是最重要的部分：



\- Send to，我们要指定你这个notification是要发给哪个服务，这里我们选择Lambda Function。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXD0r0HX6nQ8pDcKibEX8AlZ1SH3dEFQlnZR7JL0wdX2fnst8lPeibvheWg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



\- Lambda：指定想要将此事件发送给哪个lambda。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDUeKD9U483eb889MichDtdqGOAPic8SaHU1JZQ0LFCUhPopu56SoXibicdA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最后核对一下信息，没问题就点击：Save

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDYCoZGQJs2OqCumN8OGozcosts6HOicaKS9ic8AV3G7WpCbwGAkEpydwA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

好了，这个时候我们的AWS S3服务，已经和AWS Lambda服务关联好了，而且我们指定了具体将我们这个S3 bucket关联到哪个lambda function上。



接下来我们开始测试一下，这两个服务到底有没有关联成功，我们上传一个文件到我们新创建的bucket里，看看我们的lambda是否被触发了。



接上图，点击："Overview"这个tab，然后点击Upload

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXD9vByjYwt2ahP7R9picwrZd1sXlMS5z4107s0F6fJy8USgeBeMRMdfXA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



点击：Add Files按钮，或者直接将文件拖拽到Upload这个区域。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDjdHBgaHbIGgTwlEx1feOXLegqUoIUORv7JgsjicfYvteLp2fNmR4S3A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



选择想要上传的文件之后，点击Upload按钮开始上传文件。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDEZjbgXniaf0vZ879bAAeWuj37DB0pjAjlexErfs38M8N6dDHh1F0O6Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们可以看到文件上传的进程。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDzJ9yGZO7jnSEn5nxRYmow1YJkzNBZteLO4bSxJsmicRyBHRGJSLha8w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



文件上传成功：

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDrW7YDlRJJRlUYtmghFWkwyZlChiaicJaFse8FUtxbRibAXbaXjXUhhibuA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

文件上传成功后，我们打开我们创建的那个Lambda function，切换到Monitoring这个tab，这个是可视化的图表呈现，我们要去另外一个地方：View log in CloudWatch

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDBl0qcmZYQOkTcnYULG0XiavzM0BHyrpY0OSvOxbIbtIy2NKIp4ibOs0A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



点击View log in CloudWatch之后，我们就到了Log检查页面，就会发现已经有了一条log，我们点击一下。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDZy5tFmIHj5ISxDrkLVZeykaA6pFUM7mqDJZSwCrykRSYvHg1XuWeZA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在log详情页面，展开下面3条数据，我们可以看到下面的信息，目前觉得只有最后一条还可以看看，关于这个lambda的执行时间，收费时间，设置的内存大小，执行这个lambda function时使用的最大内存，初始化时间。

\- START RequestId: 544f95eb-8d86-4b66-8a12-44ec69aa4343 Version: $LATEST

\- END RequestId: 544f95eb-8d86-4b66-8a12-44ec69aa4343

\- REPORT RequestId: 544f95eb-8d86-4b66-8a12-44ec69aa4343 Duration: 1.19 ms Billed Duration: 100 ms Memory Size: 128 MB Max Memory Used: 49 MB Init Duration: 119.50 ms



![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7r7ibqMicA3tJdCibzsBCBuPkyicteToex5DfoNib8gvsicQ9vIvwOCNa9ga2IAvCOt2jsaZmm6H8MSNSww/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



好了，本篇内容就先到这里。我们已经实现：利用AWS S3的event属性，当有文件上传到S3的bucket里时，触发目标lambda function。



其实本篇我们触发的lambda function没有帮我们做任何任务，那下一篇文章，我们将重新创建一个lambda function，来达到以下目的：当有文件上传到我们的S3 bucket里时，我们的lambda function被触发，并将上传的文件重命名。