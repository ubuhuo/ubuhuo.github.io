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

那 WSGI 到底帮我们解决了什么问题？这个在 [PEP 3333][4] 中有详细的解释，我简单的说一下我的理解：在 WSGI 诞生之前，就已经存在了大量使用 Python 编写的 Web 应用框架，相应的也存在很多 Web 服务器。但是，各个 Python Web 框架和 Python Web 服务器之间不能互相兼容。夸张一点说，在当时如果想要开发一个 Web 框架说不定还得单独为这个框架开发一个 Web 服务器（而且这个服务器别的框架还不能用）。为了解决这一现象 Python 社区提交了 [PEP 333][1]，正式提出了 WSGI 这个概念。

简单的理解：只要是兼容 WSGI 的 Web 服务器和 Web 框架就能配套使用。开发服务器的程序员只需要考虑在兼容 WSGI 的情况下如何更好的提升服务器程序的性能；开发框架的程序员只需要考虑在兼容 WSGI 的情况下如何适应尽可能多业务开发逻辑（以上只是举例并非真的这样）。

WSGI 解放了 Web 开发者的精力让他们可以专注于自己需要关注的事情。

## WSGI 做了什么事情？

注：这是我是为了简练而写成了 WSGI 做了什么事情，实际上 WSGI 只是一个规范并不是实际的代码，准确的来说应该是「符合 WSGI 规范的 Web 体系做了什么事情？」

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

为了方便展示我们先来构建一个符合 WSGI 规范的 Python Web 项目示例：

```python
# server.py | wsgi server
import socket
try:
    from io import StringIO
except ImportError:
    from cStringIO import StringIO
import sys

class WSGIServer(object):
    address_family = socket.AF_INET
    socket_type = socket.SOCK_STREAM
    request_queeu_size = 1

    def __init__(self, server_address):
        # Create a listening socket
        self.listen_socket = listen_socket = socket.socket(
            self.address_family,
            self.socket_type
        )
        # 配置 socket 允许再次使用当前 address 
        listen_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        # Bind 绑定端口
        listen_socket.bind(server_address)
        # Activate 激活
        listen_socket.listen(self.request_queeu_size)
        # Get server host name and port
        host, port = self.listen_socket.getsockname()[:2]
        self.server_name = socket.getfqdn(host)
        self.server_port = port
        # Return headers set by Web framework/Wen application
        self.headers_set = []
    
    def set_app(self, application):
        self.application = application
    
    def server_forever(self):
        listen_socket = self.listen_socket
        while True:
            # New client connection
            self.client_connection, client_address = listen_socket.accept()
            # Handle one reuqest and close the client connection. Then
            # loop over to wait for another client connection
            self.handle_one_request()
    
    def handle_one_request(self):
        self.request_data = request_data = self.client_connection.recv(1024)
        # Print formatted request data a la 'curl -v'
        # print("".join("< {line}\n".format(line=line)
        #                 for line in request_data.splitlines()
        # ))
        
        self.parse_request(request_data)

        # Construct environment dictionary using request data
        # 通过 request data 构造环境变量字典
        env = self.get_environ()

        # It's time to call our application callable and get
        # back a result that will become HTTP response body
        result = self.application(env, self.start_response)

        # Construct a response and send it back to the client
        self.finish_response(result)
    
    def parse_request(self, text):
        # 取行
        request_line = text.splitlines()[0]
        # 删除指定字符
        # print(request_line)
        # print(type(request_line))
        # request_line = request_line.rstrip("\r\n")
        # Break down the request line into components
        # 打碎请求行到组件中
        (self.request_method,
         self.path,
         self.request_version
        ) = request_line.split()
    
    def get_environ(self):
        env = {}
        # The following code snippet does not follow PEP8 conventions
        # but it's formatted the way it is for demonstration purposes
        # to emphasize the required variables and their values
        # Required WSGI variables
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
        # Add necessary必要的 server headers
        server_headers = [
            ("Date", "Tue, 31 Mar 2015 12:51:48 GMT"),
            ("Server", "WSGIServer 0.2")
        ]
        self.headers_set = [status, response_headers + server_headers]
        
        # To adhere to WSGI specification the start_response must return
        # a 'write' callable. We simplicity's sake we'll ignore that detail
        # for now
        # return self.finish_response
    
    def finish_response(self, result):
        try:
            status, response_headers = self.headers_set
            response = f"HTTP/1.1 {status}\r\n"
            for header in response_headers:
                response += "{0}: {1}\r\n".format(*header)
            response += "\r\n"
            for data in result:
                response += data
            # Print formatted response data a la 'curl -v'
            print("".join(
                "> {line}\n".format(line=line)
                for line in response.splitlines()
            ))
            self.client_connection.sendall(response.encode())
        finally:
            self.client_connection.close()

SERVER_ADDRESS = (HOST, PORT) = "", 8888

def make_server(server_address, application):
    server = WSGIServer(server_address)
    server.set_app(application)
    return server

if __name__ == "__main__":
    if len(sys.argv) < 2:
        sys.exit("Provide a WSGI application object as module:callable")
    app_path = sys.argv[1]
    module, application = app_path.split(":")
    module = __import__(module)
    application = getattr(module, application)
    httpd = make_server(SERVER_ADDRESS, application)
    print(f"WSGIServer: Serving HTTP on port: {PORT}...\n")
    httpd.server_forever()
```

```python
# middleware.py | wsgi middleware.py


```

```python
# application.py | wsgi application

def app(environ, start_response):
    status = "200 OK"
    response_headers = [("Content-Type", "text/plain")]
    start_response(status, response_headers)
    return ["hello world from a simple WSGI application!\n"]
```


### WSGI 中的坑

## 解读 PEP 3333 中的某些细节

## 通过 Python wsgiref 官方库来看看官方是如何实现一个符合 WSGI 标准的 Python Web 体系

## 参考

1. [PEP 333 Python Web Server Gateway Interface v1.0][1]
2. [PEP 3333 Python Web Server Gateway Interface v1.0.1][2]
3. [知乎-方应杭-「每日一题」什么是 Web 服务器（server）][5]
4. [Skyline75489-Python WSGI学习笔记][7]

[1]: https://www.python.org/dev/peps/pep-333/
[2]: https://www.python.org/dev/peps/pep-3333/
[3]: https://www.python.org/dev/peps/pep-3333/#abstract
[4]: https://www.python.org/dev/peps/pep-3333/#original-rationale-and-goals-from-pep-333
[5]: https://zhuanlan.zhihu.com/p/22544725
[6]: https://www.python.org/dev/peps/pep-3333/#specification-overview
[7]: https://skyline75489.github.io/post/2014-9-8_python-wsgi-learning.html