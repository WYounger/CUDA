AMPQ核心概念

server：又称Broler,接收客户端的连接，实现AMPQ实体服务

conenction：连接，应用程序与broker的网路连接

channel：网络信道，几乎所有的操作都在channel中进行，channel是进行消息读写的通道。客户端可以建立多个cha nnel，每一个channel代表一个会话任务

message：消息，服务器和应用程序之间传送的数据，由properties和body组成。properties可以对消息进行修饰，比如消息的优先级，延迟等高级特性；body则就是消息实体内容

virtual host：虚拟主机，用于进行逻辑隔离，最上层的消息路由。一个virtual host里面可以有多个exchange 和queue，同一个host 里面不能有相同名称的exchange或queue

exchange:交换机，接收消息，根据路由键转发消息到绑定的队列

binding：exchange 和queue之间的虚拟链接，binding可以包含routing key

routing key：一个路由规则，虚拟机可用他来确定如何路由一个特定消息

queue：也称为message queue，消息队列，保存消息并见他们转发给消费者