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

### 一个符合 WSGI 规范的 Python Web 项目实例

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

# 根据系统导入响应的 StringIO 模块
# StringIO：用于文本 I/O 的内存数据流
try:
    from io import StringIO
except ImportError:
    from cStringIO import StringIO


class WSGIServer(object):
    request_queue_size = 1              # 请求队列长度
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
        listen_socket.listen(self.request_queue_size)
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

```python
# /path_to_code/run.py
# running Example
from server import WSGIServer
from application import application
from middleware import TestMiddleware

# 规定 server host 和 server port
server_address = (host, port) = "", 8888
# 创建 server 实例
server = WSGIServer(server_address)
# 设置本 server 对应的 middleware 以及 application
server.set_app(TestMiddleware(application))
# 输出提示性语句
print(f"WSGIServer: Serving HTTP on port: {port}...\n")
# 进入 server socket 监听循环
server.server_forever()
```

#### 运行

将四段代码分别复制到同一目录的四个文件（如果没有按照示例给出的命名记得更改一下 run 模块中相应的 import 的模块名）中。

注：以下操作默认你完全按照示例代码中给出的命名进行文件命名

1. 启动 server：`python /path_to_code/run.py`
2. 通过浏览器浏览 `127.0.0.1:8888` 查看效果
3. 通过 curl 命令 `curl -v http://127.0.0.1:8888` 查看完整输出
4. 对比 `curl -v https://baidu.com` 的输出查看区别

#### 分析

##### 代码运行流程分析

上面我根据 WSGI 协议编写了三个文件（模块）：server.py middleware.py application.py，分别对应 WSGI 里 server middleware application 这三个概念。然后通过 run.py 引入三个模块组成了一个完整的 server-middleware-application Web 程序并监听本地 8888 端口。

通过 run.py 中的代码我们能够清晰的看到一个 WSGI 类型的 Web 程序的运行流程：
1. 创建 wsgi server socket 实例对象（调用 `server.__init__` 方法）
2. 将准备好的 middleware 以及 application 对象导入给 server 实例（调用 `server.set_app` 方法）
3. 运行 server 监听指定端口（调用 `server.server_forever` 方法）

通过 server.py 中的代码能够清晰的看到一个 WSGI 类型的 Web 程序是如何处理 HTTP 请求的：
1. 通过 `server_forever` **监听到**客户端请求并**记录请求信息**
2. 调用 `handle_one_request` 方法处理此请求
    1. 通过请求 socket **获取请求数据**
    2. 通过 `parse_request` 方法将请求数据**解析**成所需格式
    3. 通过 `get_environ` 方法利用现有数据构造环境变量**字典**
    4. 将生成好的 environ 参数和 start_response 方法传给 application 对象（**也可能是 middleware 伪装的 application 对象**），并获取响应结果
    5. 将响应结果传给 `finish_response` 方法构造一个**可迭代的**响应对象返回给客户端并结束本次请求

通过 middleware.py 中的代码就能够理解一个 WSGI 中间件是如何工作的：
1. 通过在 `__init__` 方法中接收一个 application 将自己伪装成一个 server
2. 通过在 `__call__` 方法中接收 environ 和 start_response 参数将自己伪装成一个 application
通过这两点伪装 middleware 能够很好的粘合在 server 和 application 之间完成中间逻辑处理，在 [PEP 3333][10] 中指明了中间件的几点常见用途。

至于 application.py 在这里就真的只是一个简单的单文件 WSGI 应用。当然也可以尝试用写好的 server.py 和 middleware.py 对接像 Django 这样的框架，但需要对代码做一些修改，这里就不展开讨论了，有兴趣可以自己尝试。

##### 浏览器结果分析

在运行 run.py 之后使用浏览器浏览 `127.0.0.1:8888` 并查看结果如下：

![浏览器结果 1][11]
![浏览器结果 2][12]
![浏览器结果 3][13]

通过控制台可以清晰地看到响应头和响应主体的内容是符合我们预期的

##### curl 结果分析

通过 `curl http://127.0.0.1:8888` 可以看到响应主体：

![curl 结果 1][14]

通过 `curl -v http://127.0.0.1:8888` 可以看到详细的请求和响应内容：

![curl 结果 2][15]

通过 `curl -v https://baidu.com` 获取百度首页的响应内容以作比较：

![curl 结果 3][16]

可以看到目前浏览网页常用的正常请求要比自己构建的测试示例要复杂的多，这也是为什么经常使用 Web 框架而非单文件应用来处理这些请求的原因。

## 解读 PEP-3333 中的某些细节

[PEP 3333][2] 我只读到了 [Buffering and Streaming][22] 章节，并且没能很好的理解此章节所描述的东西，因此在下面的细节分析中大都是此章节之前的一些内容。

### 可迭代对象和可调用对象

可迭代对象（callable）和可迭代对象（iterable）在 PEP 3333 中最常见的两个词汇，在 WSGI 规范中它们分别代表：实现了 `__call__` 的对象和实现了 `__iter__` 的对象。

### Unicode | bytes | str

这是一组比较基础的概念：
1. Unicode 是一种字符编码标准
2. bytes 和 str 是 Python 中两种不同的数据类型

Python3 中字符串的默认类型是 str，在内存中以 Unicode 表示。如果要在网络中传输或保存为磁盘文件，需要将 str 转换为 bytes 类型。

#### Unicode | UCS | UTF

1. Unicode（万国码、国际码、统一码、单一码）是计算机科学领域里的一项业界标准。它对世界上大部分的文字系统进行了整理、编码，使得电脑可以用更为简单的方式来呈现和处理文字。Unicode 伴随着通用字符集的标准而发展，同时也以书本的形式对外发表。
2. UCS（Universal Character Set，通用字符集）是由ISO制定的ISO 10646（或称ISO/IEC 10646）标准所定义的标准字符集。
3. UTF（Unicode Transformation Format），Unicode 定义了两种映射方式：一种叫 the Unicode Transformation Format (UTF) 编码, 还有一种叫 Universal Character Set (UCS) 编码。一种编码映射一定范围（可能是子集）的 Unicode 码点（code points ）成代码值（code value）的序列。编码名字后面的数字代表一个代码值的位数（UTF使用位数，UCS 使用字节数）,UTF-8 和UTF-16是最常使用的编码。

#### bytes | str

> Python3 里面的 str 是在内存中对文本数据进行使用的，bytes 是对二进制数据使用的。
>
> str 可以 encode 为 bytes，但是 bytes 不一定可以 decode 为 tr。实际上 `bytes.decode(‘latin1’)` 可以称为 str，也就是说 decode 使用的编码决定了 `decode()` 的成败，同样的，UTF-8 编码的 bytes 字符串用 GBK 去 `decode()` 也会出错。
>
> bytes一般来自网络读取的数据、从二进制文件（图片等）读取的数据、以二进制模式读取的文本文件(.txt, .html, .py, .cpp等)
> 
> from [知乎-猿人学-Python 3 中str 和 bytes 的区别][19]

#### WSGI 中的 String

WSGI 中规定了两种 String：
1. Native String（常说的 str）用来表示 request/response headers and metadata
2. ByteString（Python3 中用 byte type 来表示）用于 request/response 的 body（例如：PUT/POST 输入和 HTML 页面输出）

在 [PEP 3333][20] 中有对这部分的详细说明。

### 三个主要组成部件

了解了以上基础概念之后再具体的看一下 WSGI 的三个主要组成部件：

#### Application/Framework | 下文简称 application

1. application 是一个**必须且只能**接收两个参数的 callable，形如 `application(environ, start_response)`。而且这两个参数只能以**位置参数**的形式被传入。
2. environ 和 start_response 只是习惯性命名，对于具体传入的对象名称没有做要求。
3. application 必须可被**多次调**用，因为所有的 server/gateway（CGI 除外）都会发出此类的重复请求。
4. [environ 是一个字典参数][21]，包含了 CGI 风格的环境变量。必须使用**内置的 Python 字典类型**（不能是子类或自定义的 UserDict），并且允许 application **以任何它想要的方式修改**。字典还包括某些 **WSGI 变量**，并且还可能包括 **server 特定的拓展参数**，它们的命名需要遵守**相应规范**。
5. start_response 参数也是一个 callable，接收两个必要的未知参数和一个可选参数，三个参数依次默认命名为：status, response_headers, exc_info，即 `start_response(status, response_headers, exc_info=None)`。
6. status 是一个状态**字符串**（str），例如：`"200 OK"`
7. response_headers 是一个描述 HTTP Response Headers 的 (header_name, header_value) **元组列表**。
8. 可选参数 exc_info 只有当 application 捕获到错误并且视图向浏览器（客户端）显示时才会调用。
9. start_response callable 必须返回一个 write(body_data) callable，这个 callable 需要一个位置参数：一个要作为 HTTP 响应体一部分的 bytestring（**注意：wirte callabel 只是为了支持某些现有框架的必要输出 API 而提供的；如果可以避免的话，新的 application/gateway 应该避免使用它**）。
10. 当 callable（**如果实现了 write 这个 callable 指的就是 write；如果没有，这个 callable 指的就是 start_response 本身**）被 server 调用时，**必须**返回一个产生零个或多个字符串的 iterable。可以通过多种方式实现，如：一个字符串列表、application 是一个 generator 函数或 application 是一个实现了 `__iter__` 的对象。**无论如何**，application 必须返回一个能够产生零个或多个字符串 iterable。
11. application 应该负责确保被写入的字符串是**适合** client 的格式的。
12. 如果 `len(iterable)` 能够被成功执行（这里的 iterable 指的是第 10 条中的 iterable）则其返回的必须是**一个 server 能够信赖的结果**。也就是说 application 返回的 iterable 如果提供了一个有效的 `__len__` 方法就必须能够获得**准确值**。
13. 如果 application 返回的 iterable 有 close 方法，server **必须**在当前请求**完成后**调用它，无论请求是否正常完成（为了支持 application 释放资源）。
14. application 应该检查其所需要的变量是否存在并对变量不存在的情况做好处理方案。

#### Server/Gateway | 下文简称 server

1. server 必须以无缓冲（unbuffered）的方式将 yielded bytestrings 传输到 client，在下一次请求之前完成每一个 bytestring 的传输。换句话说 application 应该自己实现缓存。（对于这部分我理解的不是很透彻，大多都是直译的 [PEP 3333][2]）
2. server 不能直接使用 application 返回的 iterable 的其他属性。
3. server 应该尽可能多的提供 CGI 变量。
4. 符合 WSGI 规范的 server 应该记录所提供的变量。

#### Middleware

1. middleware 是一个单独的对象，可能在一些 application 中扮演 server 同时在一些 server 中扮演 application。

### WSGI 中的坑

1. 要确定在那些地方使用 str，在那些地方使用 bytes

## Python wsgiref 官方库源码分析

可以参考我的开源库 [read-python][23] 中 practices/for_wsgiref 目录下的 [server.py][24] 文件。

在这个文件中我提取了 Python wsgiref 官方库的必要代码汇聚成一个文件实现了一个和 `wsgiref.WSGIServer` 大致同样功能的 `WSGIServer` 类。

Python wsgiref 官方库对 WSGI 规范的实现更加抽象，加上一些历史原因使得代码分布在多个官方库中，我在抽离代码的过程中学到了很多但是同样也产生了很多困惑，我在源码中使用 `TODO 疑惑 XXX` 的形式将我的困惑表达出来了，如果你感兴趣并且恰好知道解决我疑惑的方法，欢迎直接给我的代码仓库提交 Issues。

## 参考

1. [PEP 333 Python Web Server Gateway Interface v1.0][1]
2. [PEP 3333 Python Web Server Gateway Interface v1.0.1][2]
3. [知乎-方应杭-「每日一题」什么是 Web 服务器（server）][5]
4. [Skyline75489-Python WSGI学习笔记][7]
5. [Huang Huang 的博客-翻译项目系列-让我们一起来构建一个 Web 服务器][8]
6. [掘金- liaochangjiang-Python Web开发：开发wsgi中间件][9]
7. [维基百科-Unicode][17]
8. [维基百科-通用字符集][18]
9. [知乎-猿人学-Python 3 中str 和 bytes 的区别][19]
10. [Python 官方文档-术语对照表][25]


[1]: https://www.python.org/dev/peps/pep-333/
[2]: https://www.python.org/dev/peps/pep-3333/
[3]: https://www.python.org/dev/peps/pep-3333/#abstract
[4]: https://www.python.org/dev/peps/pep-3333/#original-rationale-and-goals-from-pep-333
[5]: https://zhuanlan.zhihu.com/p/22544725
[6]: https://www.python.org/dev/peps/pep-3333/#specification-overview
[7]: https://skyline75489.github.io/post/2014-9-8_python-wsgi-learning.html
[8]: https://mozillazg.com/tag/rang-wo-men-yi-qi-lai-gou-jian-yi-ge-web-fu-wu-qi.html
[9]: https://juejin.im/post/5ccb8bb8f265da03981fd577
[10]: https://www.python.org/dev/peps/pep-3333/#middleware-components-that-play-both-sides
[11]: https://img.blanc.site//wiki/img/20200630114909.png
[12]: https://img.blanc.site//wiki/img/20200630114908.png
[13]: https://img.blanc.site//wiki/img/20200630114910.png
[14]: https://img.blanc.site//wiki/img/20200630115755.png
[15]: https://img.blanc.site//wiki/img/20200630115754.png
[16]: https://img.blanc.site//wiki/img/20200630115753.png
[17]: https://zh.wikipedia.org/wiki/Unicode
[18]: https://zh.wikipedia.org/wiki/%E9%80%9A%E7%94%A8%E5%AD%97%E7%AC%A6%E9%9B%86#Unicode%E5%92%8CISO_10646%E7%9A%84%E5%85%B3%E7%B3%BB
[19]: https://zhuanlan.zhihu.com/p/56901822
[20]: https://www.python.org/dev/peps/pep-3333/#unicode-issues
[21]: https://www.python.org/dev/peps/pep-3333/#environ-variables
[22]: https://www.python.org/dev/peps/pep-3333/#buffering-and-streaming
[23]: https://github.com/ryomahan/read-python
[24]: https://github.com/ryomahan/read-python/blob/master/practices/for_wsgiref/server.py
[25]: https://docs.python.org/zh-cn/3/glossary.html