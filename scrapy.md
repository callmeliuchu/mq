1.组件
　　　1.1　scrapy engine
         引擎负责控制数据流动在系统所有组件中流动，并在响应动作发生时触发事件
    1.2　schedule
         调度器从引擎接受request并将他们入队，引擎之后请求他们可以拿到数据
    1.3  downloader
         下载器负责页面数据并提供给引擎，而后提供给spider
    1.4　　spiders
         spider是用户编写的用于分析response并且提取item的额外的url类,每个spider负责特定的网站
    1.5  item pipeline
         负责被spider提取出来的item,典型的处理有清理，验证以及持久化
    1.6  downloader middlewares
         下载器中间件实在引擎和下载器之间的钩子，处理downloader传递给引擎的response,通过自定义代码扩展scrapy功能
    1.7  spider middlewares
          是引擎和spider之间的钩子，处理spider的输入的response和输出的item,requests
２．数据流动
   1.引擎打开一个网站(open domain),找到处理该网站的spider并且向该spider请求第一个要爬取的url(s)
   2.引擎从spider中获取第一个要爬取的url并且在调度器以request调度
   3.引擎向调度器请求下一个要爬取的url
   4.调度器返回下一个要爬取的url给引擎，引擎将url通过下载中间件(请求request)转发给downloader
   5.一旦页面下载完成，下载器生成该页面一个response,并且返回给中间件发送给引擎
   6.引擎从下载器中获取的response通过spider中间件发送给spider处理
   ７.spider处理response并且返回取到的item和新的request给引擎
   8.引擎将返回的爬到的item给item pipelines,将request给调度器
   9.从第二步重复到调度器中没有更多的request，引擎关闭该网站