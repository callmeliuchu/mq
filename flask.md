flask current_app 等虽然看起来是全局变量，但是实际上是线程隔离的。flask的线程隔离机制是:为每一个线程的数据放入map映射
                  加入程序有一个请求，那么其请求会将所有信息都打包，打包形成一个环境，flask将这个环境成为请求上下文


wsgi协议：web server gateway interface 规范。  要实现wsgi协议，必须同时实现web serve 和 web application ，在当前运行在wsgi协议智商的web框架有
    bottle,flask,django

uwsgi 是通信协议


WSGI协议包括两部分，server 和　application


WSGI server 负责从客户端接收请求，将request转发给application 将application返回的response返回给客户端

WSGI application 接收由server 转发的response,处理请求，并将处理结果返回给server