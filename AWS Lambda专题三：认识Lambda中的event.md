## AWS Lambda专题三：认识Lambda中的event

原创 Ben Jia [BPShare](javascript:void(0);) *今天*

来自专辑

AWS

上一篇文章我们已经了解了如何通过AWS S3的event去触发AWS Lambda function执行，但是我们的AWS Lambda function并没有去执行任何与AWS S3服务的动作。从本节开始，我们将在AWS Lambda function去操作AWS S3的bucket和bucket中的文件（object）。



在我们用AWS Lambda操作S3之前，我们首先来认识一下AWS Lambda默认定义的函数lambda_handler(event, context)中的event是个什么神马。



如果有同学用上一篇中的代码，直接点击Test按钮，会出现下面的错误：

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oLdGlCRzpENuJnW4tOBQUUTGib8lcxg7begUFwwJKpq5P3qUgzauFqOGuibLtXiaQED1PQnDRzfDDXQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

原因是我们在保存Event时，我们的输入是下面的内容：

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oLdGlCRzpENuJnW4tOBQUUVJiadWuS6SicuCbRPOQ68iaBBpGkVZ72xDa5Tx79fWbUEB1DFialFTJWIw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



而在定义的lambda_handler(event, context)函数里，event的格式是有要求的。那么event这个参数到底是什么格式的呢？



![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oLdGlCRzpENuJnW4tOBQUUVctRGMeDt7Pia3vApbVpNUy5icPQ0ibkvIcHWqWXzdwxLF0TuUNaIfohQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



既然我们的runtime选择的是Python，那么就离不开别人已经帮我们造好的轮子，因为要操作的是AWS S3服务，而AWS S3服务是属于AWS服务的一种，所以我们就需要AWS SDK for Python，这个轮子叫：boto3，所以首先我们要导入boto3这个包：

```python
import json
import boto3
```

关于boto3的用法，详见官方文档：

**https://boto3.amazonaws.com/v1/documentation/api/latest/index.html**



接着我们需要声明一下我们是需要操作S3的服务：

```python
import json
import boto3

s3 = boto3.resource('s3')
def lambda_handler(event, context):    
    #TODOIMPLEMENT    
    return {        
        'statusCode': 200,        
        'body': json.dumps('Hello from Lambda!')    
    }
```



下面我们就来看看代码中定义的lambda_handler(event, context)函数中的event到底是个什么样子，我们来直接打印一下：

```python
import json
import boto3

s3 = boto3.resource('s3')

def lambda_handler(event, context):    
    #TODOIMPLEMENT    
    print(event)       
    return {        
        'statusCode': 200,        
        'body': json.dumps('Hello from Lambda!')    
    }
```



这里有个坑，前面介绍过，你需要利用AWS S3的bucket来触发这个lambda function，才能看到真正的event格式是什么样子的。具体如何触发呢？对，就是上传一个文件到我们上篇文章中的bucket。具体上传文件的步骤这里不再累述，如果有需要，请移步：[《AWS Lambda专题二：AWS S3触发Lambda function》](http://mp.weixin.qq.com/s?__biz=MzI5NTU2MzM0NQ==&mid=2247483852&idx=1&sn=db7060b27e7168b2f1498a57dfbbdb5f&chksm=ec50f8f2db2771e49e7e024fc796c24ae9dc4953c175b2099498567f2caede9d206f0d8559b9&scene=21#wechat_redirect)



文件上传成功之后，在我们的lambda代码页面的上方，有一个Monitoring tab，点进去，有一个：View logs in CloudWatch连接，点击这个连接，

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDBl0qcmZYQOkTcnYULG0XiavzM0BHyrpY0OSvOxbIbtIy2NKIp4ibOs0A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们就来到了Logs页面，每当lambda function被触发执行的时候，就会产生一条log，想要查看某一条log的具体信息，直接点进去就可以了。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7qD0vaARAngs9RDzd6ia0KXDZy5tFmIHj5ISxDrkLVZeykaA6pFUM7mqDJZSwCrykRSYvHg1XuWeZA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



点击刚才我们上传文件之后产生的那条log，看到了吧？event这个参数到底是个什么鬼。是一个dict类型，而且里面又嵌套了list，list里又是list。我第一次看的时候都晕了，看的头大。

![img](https://mmbiz.qpic.cn/mmbiz_png/cK5sfXRSz7oLdGlCRzpENuJnW4tOBQUUHB5RzwytUzuWdWSjwPTcxG1DCHj1IeHhlNOIXWJfLaVtiaOMKDrFDMQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



网上找来了一个event的例子，大概是这个样子：

```json
{  
    "Records":[    
        {      
            "eventVersion":"2.0",      
            "eventSource":"aws:s3",      
            "awsRegion":"us-west-2",      
            "eventTime":"1970-01-01T00:00:00.000Z",      
            "eventName":"ObjectCreated:Put",      
            "userIdentity":{
                "principalId":"AIDAJDPLRKLG7UEXAMPLE"},      
            "requestParameters":{
                "sourceIPAddress":"127.0.0.1"},      
            "responseElements":{
                "x-amz-request-id":"C3D13FE58DE4C810",        
                "x-amz-id-2":"FMyUVURIY8/IgAtTv8xRjskZQpcIZ9KG4V5Wp6S7S/JRWeUWerMUE5JgHvANOjpD"},      
            "s3":{
                "s3SchemaVersion":"1.0",
                "configurationId":"testConfigRule",
                "bucket":{
                    "name":"sourcebucket",
                    "ownerIdentity":{
                        "principalId":"A3NL1KOZZKExample"},
                    "arn":"arn:aws:s3:::sourcebucket"},
                "object":{ 
                    "key":"HappyFace.jpg",
                    "size":1024,
                    "eTag":"d41d8cd98f00b204e9800998ecf8427e",
                    "versionId":"096fKKXTRTtl3on89fVO.nfljtsv6qko"}
            }
        }
    ]
}
```



so，首先，这个event是一个字典，这个字典里只有一个key，名字是Records，value是一个列表。那我们也来打印一下：

```python
import json
import boto3

s3 = boto3.resource('s3')
def lambda_handler(event, context):
    print(event)
    print(event.get('Records'))
    #或者用event['Records']
    return {        
        'statusCode': 200,        
        'body': json.dumps('Hello from Lambda!')     
    }
```



再去Log页面看一下最新的那条log详细信息，多了一条：就是只有[...]那一条，而且这个list里只有一个值，这个值又是一个字典，了解这一点很重要（对于Python新手来说，比如说我）



```json
[    
        {      
            "eventVersion":"2.0",      
            "eventSource":"aws:s3",      
            "awsRegion":"us-west-2",      
            "eventTime":"1970-01-01T00:00:00.000Z",      
            "eventName":"ObjectCreated:Put",      
            "userIdentity":{
                "principalId":"AIDAJDPLRKLG7UEXAMPLE"},      
            "requestParameters":{
                "sourceIPAddress":"127.0.0.1"},      
            "responseElements":{
                "x-amz-request-id":"C3D13FE58DE4C810",        
                "x-amz-id-2":"FMyUVURIY8/IgAtTv8xRjskZQpcIZ9KG4V5Wp6S7S/JRWeUWerMUE5JgHvANOjpD"},      
            "s3":{
                "s3SchemaVersion":"1.0",
                "configurationId":"testConfigRule",
                "bucket":{
                    "name":"sourcebucket",
                    "ownerIdentity":{
                        "principalId":"A3NL1KOZZKExample"},
                    "arn":"arn:aws:s3:::sourcebucket"},
                "object":{ 
                    "key":"HappyFace.jpg",
                    "size":1024,
                    "eTag":"d41d8cd98f00b204e9800998ecf8427e",
                    "versionId":"096fKKXTRTtl3on89fVO.nfljtsv6qko"}
            }
        }
    ]
```



由于这个list里只有一个值，所以我们来获取一下：

```python
import json
import boto3

s3 = boto3.resource('s3')

def lambda_handler(event, context):    
    print(event)    
    print(event.get('Records'))    
    #或者用event['Records']    
    print(records_content[0])    
    return {        
        'statusCode': 200,        
        'body': json.dumps('Hello from Lambda!')     
    }
```



再去Log页面看一下最新的那条log详细信息，多了一条{...}的内容，这个字典里面就很好看了，要么是单纯的key，value，要么是key里面又嵌套字典：

```json
{      
            "eventVersion":"2.0",      
            "eventSource":"aws:s3",      
            "awsRegion":"us-west-2",      
            "eventTime":"1970-01-01T00:00:00.000Z",      
            "eventName":"ObjectCreated:Put",      
            "userIdentity":{
                "principalId":"AIDAJDPLRKLG7UEXAMPLE"},      
            "requestParameters":{
                "sourceIPAddress":"127.0.0.1"},      
            "responseElements":{
                "x-amz-request-id":"C3D13FE58DE4C810",        
                "x-amz-id-2":"FMyUVURIY8/IgAtTv8xRjskZQpcIZ9KG4V5Wp6S7S/JRWeUWerMUE5JgHvANOjpD"},      
            "s3":{
                "s3SchemaVersion":"1.0",
                "configurationId":"testConfigRule",
                "bucket":{
                    "name":"sourcebucket",
                    "ownerIdentity":{
                        "principalId":"A3NL1KOZZKExample"},
                    "arn":"arn:aws:s3:::sourcebucket"},
                "object":{ 
                    "key":"HappyFace.jpg",
                    "size":1024,
                    "eTag":"d41d8cd98f00b204e9800998ecf8427e",
                    "versionId":"096fKKXTRTtl3on89fVO.nfljtsv6qko"}
            }
}
```



好了，到这一层，才是我们真正感兴趣的数据，现在我们获取一下eventName的值：

```python
records_content_dict = records_content[0]
print(records_content_dict.get('eventName'))
```



再往下看，重中之重的数据在s3那里，我们再来获取一下：

```python
records_content_dict = records_content[0]
print(records_content_dict.get('s3'))
```



再深入一下，我们可以获取bucket的相关信息：

```python
records_content_dict = records_content[0]
s3_dict_content = records_content_dict.get('s3')
print(s3_dict_content.get('bucket'))
```



和bucket里面的文件（object）信息：

```python
records_content_dict = records_content[0]
s3_dict_content = records_content_dict.get('s3')
print(s3_dict_content.get('object'))
```



最后，我们想要的文件名就在这个object里，finally,终于知道了如何获取上传文件的文件名了：

```python
records_content_dict = records_content[0]
s3_dict_content = records_content_dict.get('s3')
s3_object_content = s3_dict_content.get('object')
s3_object_file_name = s3_object_content.get('key')
```



event感觉像是一个百宝箱，这个百宝箱还有夹层，你需要用正确的姿势去开开它，才能得到你想要的东西。



本篇内容就先讲到这里，下一篇我们将开始操作S3 bucket中的文件，实现有文件上传到S3 bucket时，自动触发AWS Lambda function脚本执行，重命名上传文件。