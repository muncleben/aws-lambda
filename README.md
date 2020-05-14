# AWS Lambda service的学习笔记

什么是AWS Lambda？

AWS官方是这么介绍滴： AWS Lambda is a compute service that lets you run code without provisioning or managing servers. AWS Lambda executes your code only when needed and scales automatically, from a few requests per day to thousands per second.

更多详细介绍，请参考AWS Lambda官方文档: https://docs.aws.amazon.com/lambda/latest/dg/welcome.html

所以AWS Lambda是AWS的一个云服务，而且是一个serverless的服务，你只管写代码实现你想要的功能就好，其它的你不需要管，都交给AWS来负责了。目前AWS Lambda支持的编程语言有：

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


