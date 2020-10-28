总结工作中使用的三个消息队列，希望通过自己的的描述能够更深入理解这三个mq


1.在之前处理用户画像的的时候使用的是kafka,当时使用kafka的场景是收集用户行为日志的数据进行统计:
  1.1,说明kafka适用于行为日志的处理，因为行为日志的消息丢失相对来说是不是很重要的，所以kafka没有消息确认机制
  1.2,kafka读取消息是根据topic读取,重启的时候可以从之前断开的offset读取
  1.3,kafka是流式消息，只能从最近七天到目前未知的消息.实时性


2.rabbitmq是在做智能陪练的时候用到的，当时用的python客户端，代码主要逻辑是从rabbitmq对应的topic的队列拉取消息，拉取下来的时候可以默认拉下即提交，
  也可以设置成不提交，拉下的消息带有消息的确认机制方法，在业务处理成功的时候可以提交，否则不提交，多次处理，如果任然不成功，再提交，该消息就会被删除


3.rocketmq也适用于智能陪练，因为公司基础组件的更换，把rabbitmq换成了rocketmq,所以代码也要更换。使用的感受是，rocketmq客户端回去请求rocketserver
  端的nameserver,然后nameserver会返回一个broker节点,获取消息就是从这个broker节点获取消息


4.以上是在代码层使用这些mq组件的感受。接下来总结从网上学习的这三个消息组件的内容。
  1.先总结rocketmq:
         1.producer,
           消息生产者，位于用户的进程内，producer通过nameserver获取所有的broker的信息,根据负载均衡策略选择将消息发到哪个broker,然后调用broker
         2.producer group
           生产者组,简单来说就是多个发送同一类消息的生产者称之为一个生产者组
         3.consumer
           消息消费者，位于用户进程内，consumer通过nameserver获取所有broker的路由信息后，向broker发送pull请求获取消息,consumer可以有两种模式启动，
           广播和集群，广播模式是发给所有consumer,集群模式是发给一个consumer
         4.consumer group
           消费者组和生产者类似，消费同一类消息的多个consumer实例组成一个消费者组
         5.topic
           topic用于将消息按照主题划分，producer将消息发送给指定的topic,consumer订阅该topic就可以收到这条消息，topic是一个逻辑上的概念，消息存储不会按
           topic分开
         6.message
           代表一条消息，使用messageid唯一标示，用户在发送的时候可以设置messagekey,标语之后跟踪，一个message必须指定topic,相当于寄信地址，message还有一个tag
           可以设置，方便消费端用tag进行过滤
         7.tag
           标签可以认为是对topic的进一步细化，一般在相同业务模块中通过引入标签来标记不同用途的消息
         8.broker
           broker是roketmq的核心模块，负责接收并存储消息，同时提供push/pull借口来将消息给consumer.consumer可以从master或slave读取数据，多个主/从组成broker
           集群，集群内的master节点之间不做数据交互。broker同事提供消息查询功能，可以通过messageid和messagekey来查询消息，broker会将自己的topic配置信息实时同步到nameserver
         9.queue
           topic和queue是一对多的关系，一个topic可以包含多个queue,主要用于负载均衡，发消息时，用户只指定topic,producer会根据topic的路由信息选择发到哪个queue上,
           consumer订阅消息时，会根据负载均衡策略决定订阅哪些queue的消息
         10.offset
           rocketmq在存储消息时会为每个topic下的queue生成一个索引文件，每个queue对应一个offset记录当前的消息数
         11.nameserver
           nameserver可以看做是rocketmq的注册中心，它管理两部分的数据，集群topic-queue的路由配置，broker的实时配置信息，其他模块通过nameserver提供的接口获取topic的
           配置和路由信息
         producer/consumer:通过查询接口获取topic对应的broker地址信息
         broker:注册配置信息到nameserver,实时更新topic信息到nameserver
         12.queue不是真正存储message的地方，真正存储message的地方是在commitlog
  2.总结rabbitmq架构
  
  
  3.总结kafka
         1.kafka 是发布订阅消息传传递,发布者发送到topic的消息，只有订阅了topic的订阅者才会收到消息
         
         