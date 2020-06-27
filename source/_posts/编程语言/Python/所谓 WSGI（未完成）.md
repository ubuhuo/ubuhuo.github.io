---
title: 所谓 WSGI
date: 2020/06/24
categories:
  - 编程语言
  - Python
tags:
  - WSGI
copyright: true
---

## 注意

1. 如果你也想阅读 WSGI 相关的 PEP 规范，建议直接阅读 [PEP 3333][2]，因为 [PEP 3333][2] 对 [PEP 333][1] 是向下兼容的，也可以说 [PEP 3333][2] 是对 [PEP 333][1] 的补充。

## 何为 WSGI？

> This document specifies a proposed standard interface between web servers and Python web applications or frameworks, to promote web application portability across a variety of web servers.
> 
> 本文档详细描述了一个建议用在 Web 服务器和 Python Web 应用或框架之间的标准接口，以提升 Web 应用在各类 Web 服务器之间的可移植性。
> 
> from [PEP 3333][3]

从 [PEP 3333][3] 的这段总结来看，WSGI 就是一个 Python 官方建议用在 Web 服务器和 Python Web 应用框架之间的标准接口。

### 何为 Web 服务器

首先，什么是服务器（server）？
一般来说，server 有两重意思：
1. 有时 server 表示硬件，也就是一台机器，也可称为「主机」；
2. 更多的时候 server 表示软件程序，这种程序主要用来对外提供某种服务，比如：邮件服务、FTP 服务、数据库服务、网页服务等等。

作为开发者，一般提到 server 时指的都是后者，即一个长时间运行的软件程序。

所以，什么是 Web Server？
通俗的来讲 Web Server 就是一个提供 Web 服务的应用程序。
常见的符合 WSGI 规范的 Web Server 有 uWSGI、gunicorn 等等。

### 何为 Web 应用或框架

Web 框架在如今是比较常见的，比较知名的 Python Web 框架有：Django、Flask、Pyramid等等。反倒是 Web 应用不太常见，（个人理解）一般情况下只有在本地测试的时候会写一些简单的 Python Web 应用，平时的开发大多还是使用开源（或公司内部）的 Web 框架。

## 为什么需要 WSGI

作为一个近两年刚接触到 Python Web 编程的新手，在日常的编程过程中完全没有见过所谓的 WSGI，但是我依然可以写好一个完整的 Web 应用，这是为什么？WSGI 有存在的必要嘛？

答案肯定是：有存在的必要。

首先解释一下为什么我在过去两年的过程中没有见过 WSGI 却依旧可以进行 Web 编程：因为现在的大多数框架都已经帮我们将 WSGI 标准封装在框架底层。甚至，我用的 Django REST Framework 框架连 HTTP Request 和 HTTP Response 都帮我封装好了。所以，就算我完全不了解 WSGI 这种偏底层的协议也能够进行日常的 Web 开发。

那 WSGI 到底解决了什么问题？这个在 [PEP 3333][4] 中有详细的解释，简单的说一下我的理解：在 WSGI 诞生之前，就已经存在了大量使用 Python 编写的 Web 应用框架，相应的也存在很多 Web 服务器。但是，各个 Python Web 框架和 Python Web 服务器之间不能互相兼容。夸张一点说，在当时如果想要开发一个 Web 框架说不定还得单独为这个框架开发一个 Web 服务器（而且这个服务器别的框架还不能用）。为了解决这一现象 Python 社区提交了 [PEP 333][1]，正式提出了 WSGI 这个概念。

简单的理解：只要是兼容 WSGI 的 Web 服务器和 Web 框架就能配套使用。开发服务器的程序员只需要考虑在兼容 WSGI 的情况下如何更好的提升服务器程序的性能；开发框架的程序员只需要考虑在兼容 WSGI 的情况下如何适应尽可能多业务开发逻辑（以上只是举例并非真的这样）。

WSGI 解放了 Web 开发者的精力让他们可以专注于自己需要关注的事情。

## WSGI 做了什么事情？

注：为了简练而写成了 WSGI 做了什么事情，实际上 WSGI 只是一个规范并不是实际的代码，准确的来说应该是「符合 WSGI 规范的 Web 体系做了什么事情？」

上面已经提到，WSGI 通过规范化 Web 框架和 Web 服务器之间的接口，让兼容了 WSGI 的框架和服务器能够自由组合使用……

所以，WSGI 究竟做了什么，让一切变得如此简单？

在 [PEP 3333][6] 中对 WSGI 进行了一段简单的概述，这里我结合看过的 [一篇博文][7] 进行简单的概括：

（简单来说）WSGI 将 Web 分成了三个部分，从上到下分别是：Application/Framework, Middleware 和 Server/Grageway，各个部分之间高度解耦尽可能的做到不互相依赖。

1. （通常情况下）客户端（一般为浏览器）会向 Server 发送 HTTP 请求以获取数据。
2. 符合 WSGI 规范的 Server 在接收到请求之后会调用指定的符合 WSGI 规范的 Web Application，并传入 environ 和 start_response 两个参数（并不强制命名，只是一般会这么命名）。
3. Web Application 在接收到请求后会生成一个打包好的 HTTP Response 传给 start_response。
4. Server 会将 HTTP Response 进行汇总待请求处理完且没有错误时将整个 HTTP Response 内容返回给客户端。

Middleware 属于三个部分中最为特别的一个，对于 Server 他是一个 Application，对于 Application 它是一个 Server。通俗的来说就是 Middleware 面对 Server 时能够展现出 Application 应有的特性，而面对 Application 时能够展现出 Server 应有的特性，由于这一特点 Middleware 在整个协议中起到了承上启下的功能。在现实开发过程中，还可以通过嵌套 Middleware 以实现更强大的功能。

## WSGI 是如何工作的？

通过上一小节能够大概的了解到 WSGI 在一次完整的请求中究竟做了什么。下面再来介绍一下一个完整的 WSGI Web 体系是如何工作的。

### 一个符合 WSGI 规范的最小 Python Web 项目实例

为了方便展示先来构建一个符合 WSGI 规范的 Python Web 项目示例：

#### 源码

注：示例基于 Python3

```python
# 本示例代码改自参考文章 5：
# Huang Huang 的博客-翻译项目系列-让我们一起来构建一个 Web 服务器
# /path_to_code/server.py
# Examples of wsgi server
import sys
import socket
# 分别从应用模块和中间件模块中引入相应模块
from application import application
from middleware import TestMiddleware

# 根据系统导入响应的 StringIO 模块
# StringIO：用于文本 I/O 的内存数据流
try:
    from io import StringIO
except ImportError:
    from cStringIO import StringIO


class WSGIServer(object):
    request_queeu_size = 1              # 请求队列长度
    address_family = socket.AF_INET     # 设置地址簇
    socket_type = socket.SOCK_STREAM    # 设置 socket 类型

    def __init__(self, server_address):
        # Server 初始化方法（构造函数）
        # Create a listening socket
        self.listen_socket = listen_socket = socket.socket(
            self.address_family,
            self.socket_type
        )
        # 设置 socket 允许重复使用 address
        listen_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        # Bind 绑定端口
        listen_socket.bind(server_address)
        # Activate 激活
        listen_socket.listen(self.request_queeu_size)
        # 获取并记录 server host 和 port
        host, port = self.listen_socket.getsockname()[:2]
        self.server_name = socket.getfqdn(host)
        self.server_port = port
        # Return headers set by Web framework/application
        self.headers_set = []
    
    def set_app(self, application):
        # 将传入的 application 设置为实例属性
        self.application = application
    
    def server_forever(self):
        # 开启 server 循环函数
        listen_socket = self.listen_socket
        while True:
            # 获取 client socket 参数 | client_connection 是 client socket 实例
            # 这里会创建一个阻塞，直到接受到 client 连接为止
            self.client_connection, client_address = listen_socket.accept()
            # 调用 handle_one_request 方法处理一次请求并关闭 client 连接然后继续等待新的连接进入
            self.handle_one_request()
    
    def handle_one_request(self):
        # 处理请求的入口方法 | 用来处理一次请求
        # 从 client socket 中获取 request data
        self.request_data = request_data = self.client_connection.recv(1024)
        
        # 调用 parse_request 方法， 传入接收到的 request_data 并对其进行解析
        self.parse_request(request_data)

        # 通过已有数据构造环境变量字典
        environ = self.get_environ()

        # 调用 application，传入已经生成好的 environ 和 start_response，返回一个可迭代的 Response 对象
        result = self.application(environ, self.start_response)

        # 调用 finish_response 方法，构造一个响应并返回给客户端
        self.finish_response(result)
    
    def parse_request(self, text):
        # 取行
        request_line = text.splitlines()[0]
        # 打碎请求行到组件中
        (self.request_method,
         self.path,
         self.request_version
        ) = request_line.split()
    
    def get_environ(self):
        env = {}
        env["wsgi.version"] = (1, 0)
        env["wsgi.url_scheme"] = "http"
        env["wsgi.input"] = StringIO(self.request_data.decode("utf-8"))
        env["wsgi.errors"] = sys.stderr
        env["wsgi.multithread"] = False
        env["wsgi.multiprocess"] = False
        env["wsgi.run_once"] = False
        # Required CGI variables
        env["REQUEST_METHOD"] = self.request_method
        env["PATH_INFO"] = self.path.decode("utf-8")
        env["SERVER_NAME"] = self.server_name
        env["SERVER_PORT"] = str(self.server_port)
        return env
    
    def start_response(self, status, response_headers, exc_info=None):
        # 按照 WSGI 规范提供一个 start_response 给 application
        # Add necessary必要的 server headers
        server_headers = [
            ("Date", "Tue, 31 Mar 2020 12:51:48 GMT"),
            ("Server", "WSGIServer 0.2")
        ]
        self.headers_set = [status, response_headers + server_headers]
        
        # 按照 WSGI 协议，应该在这里返回一个 write()，但这里为了简便就省略了
        # 会在后续分析 wsgiref 源码时提及此处
    
    def finish_response(self, result):
        # 通过现有参数整理出一个响应体
        try:
            status, response_headers = self.headers_set
            # 响应第一部分：HTTP 协议以及状态码
            response = f"HTTP/1.1 {status}\r\n"
            # 响应第二部分：将生成好的响应头递归式的传入响应体内
            for header in response_headers:
                response += "{0}: {1}\r\n".format(*header)
            # 通过 \r\n 进行空行
            response += "\r\n"
            # 响应第三部分：将响应主题信息追加到响应体内
            for data in result:
                response += data
            # 通过 senall 将响应发送给客户端
            # 注意：在 Python3 下，如果你构建的响应体为 str 类型，需要进行 encode 转换为 bytes
            self.client_connection.sendall(response.encode())
        finally:
            # 关闭连接
            self.client_connection.close()

SERVER_ADDRESS = (HOST, PORT) = "", 8888

def make_server(server_address, application):
    server = WSGIServer(server_address)
    # 注意这里的调用过程，需要通过中间件模块包裹应用模块
    server.set_app(TestMiddleware(application))
    return server

if __name__ == "__main__":
    # 创建 WSGI server
    httpd = make_server(SERVER_ADDRESS, application)
    print(f"WSGIServer: Serving HTTP on port: {PORT}...\n")
    # 进入循环，捕获请求
    httpd.server_forever()
```

```python
# /path_to_code/middleware.py
# Examples of wsgi middleware
class TestMiddleware(object):

    def __init__(self, application):
        self.application = application

    def core(self, environ, start_response):
        old_response = self.application(environ, start_response)
        new_response = old_response + ["middleware add this message\n"]
        return new_response

    def __call__(self, environ, start_response):
        return self.core(environ, start_response)
```

```python
# /path_to_code/application.py
# Examples of wsgi application
def application(environ, start_response):
    status = "200 OK"
    response_headers = [("Content-Type", "text/plain")]
    start_response(status, response_headers)
    return ["hello world from a simple WSGI application!\n"]
```

#### 运行

将三段代码分别复制到同一目录的三个文件（如果没有按照示例给出的命名记得更改一下 server 模块中相应的 import 的模块名）中。

注：以下操作默认你完全按照示例代码中给出的命名进行文件命名

1. 启动 server：`python /path_to_code/server.py`
2. 通过浏览器浏览 `127.0.0.1:8888` 查看效果
3. 通过 curl 命令 `curl -v http://127.0.0.1:8888` 查看完整输出
4. 对比 `curl -v https://baidu.com` 的输出查看区别

#### 分析

1. 原理分析
2. 浏览器结果分析
3. curl 结果分析

## 解读 PEP-3333 中的某些细节

### WSGI 中的坑

## Python wsgiref 官方库源码分析

## 参考

1. [PEP 333 Python Web Server Gateway Interface v1.0][1]
2. [PEP 3333 Python Web Server Gateway Interface v1.0.1][2]
3. [知乎-方应杭-「每日一题」什么是 Web 服务器（server）][5]
4. [Skyline75489-Python WSGI学习笔记][7]
5. [Huang Huang 的博客-翻译项目系列-让我们一起来构建一个 Web 服务器][8]
6. [掘金-
liaochangjiang-Python Web开发：开发wsgi中间件][9]

[1]: https://www.python.org/dev/peps/pep-333/
[2]: https://www.python.org/dev/peps/pep-3333/
[3]: https://www.python.org/dev/peps/pep-3333/#abstract
[4]: https://www.python.org/dev/peps/pep-3333/#original-rationale-and-goals-from-pep-333
[5]: https://zhuanlan.zhihu.com/p/22544725
[6]: https://www.python.org/dev/peps/pep-3333/#specification-overview
[7]: https://skyline75489.github.io/post/2014-9-8_python-wsgi-learning.html
[8]: https://mozillazg.com/tag/rang-wo-men-yi-qi-lai-gou-jian-yi-ge-web-fu-wu-qi.html
[9]: https://juejin.im/post/5ccb8bb8f265da03981fd577