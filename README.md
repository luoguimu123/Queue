# Queue

*本项目实现以ZooKeeper为基础，实现一个简单的队列以及分布式锁，其中队列实现了2种基本的信息传输模型，分别是基本推拉模型和订阅模型*

用户使用时需要的重要的类说明：


1.分布式锁

DistributedLock：分布式锁的实现类，其主要方法如下：
  
  	public DistributedLock(String root, String topic, String url) 其中root为自定义的根目录，必须提前设置好，topic是为整个队列系统提供的子目录节点，可以理解为队列中的Topic。url为Zookeeper的连接地址(host+port);
  
  	public boolean tryLock() 尝试是否能获得锁，返回true表示现在可以尝试获得锁。
  
  	public void lock() 上锁，一旦上锁后，其他对同root，topic的节点上锁的锁无法锁定。
  
  	public void unlock() 解锁，解锁必须在上锁后使用。
  
  	public void close()关闭，在不在需要锁的实例的时候调用。
  
2.推拉消息队列
  
PushProvider：队列的Provider，这里表示的是一个push/pull模式中的Push方式的Provider

 	public PushProvider(String topic, String url) :Provider的初始化方法，topic表示主题，url表示zookeeper的地址。
  
  	public void push(String value) ：发送一个字符串
  
 	public void push(List<String> list)：发送一堆字符串
  
PullConsumer：队列的Consumer，这里表示的是一个push/pull模式的Pull方式的Consumer

  	public PullConsumer(String topic, String url)：初始化，参数意义同Provider。
  
  	public List<String> pull()：拉取数据

3.订阅消息队列

TopicProvider: 队列的Provider，这里表示的是一个订阅模式队列的发布方。

  	public TopicProvider(String topic, String url) :Provider的初始化方法，topic表示主题，url表示zookeeper的地址。
  
  	public void publish(String value)   发布一个字符串
  
  	public void publish(List<String> list) 发布一系列的字符串
  
TopicConsumer: 队列的Consumer，这里表示的是一个订阅模式队列的订阅方。

	public TopicConsumer(String topic, String url, ConsumerHandler handler)Consumer的构造函数，其中topic和url的含义同上，handler表示的是一个回调接口的实现类，每当收到消息的时候会自动回调这个类的handle方法，其接口签名如下：
	public interface ConsumerHandler{
        void handle(List<String> list);
    }
    
    public void close();在不需要再订阅消息的时候使用。























