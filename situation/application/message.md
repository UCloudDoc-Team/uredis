

## 构建实时消息系统

Redis的Pub/Sub系统可以构建实时的消息系统，比如很多开发人员用Pub/Sub构建实时聊天系统。

``` java
import redis.clients.jedis.*;
import java.util.Date;
import org.apache.commons.lang3.time.DateFormatUtils;
import org.apache.commons.lang3.RandomStringUtils;
class PrintListener extends JedisPubSub{
    @Override
    public void onMessage(String channel, String message) {
        String time = DateFormatUtils.format(new Date(), "yyyy-MM-dd HH:mm:ss");
        System.out.println("message receive:" + message + ",channel:" + channel +
        "..." + time);
        //此处我们可以取消订阅
        if(message.equalsIgnoreCase("quit")){
            this.unsubscribe(channel);
        }
    }
}
class PubClient {
    private Jedis jedis;
    public PubClient(String host,int port){
        jedis = new Jedis(host,port);
    }
    public void pub(String channel,String message){
        jedis.publish(channel, message);
    }
    public void close(String channel){
        jedis.publish(channel, "quit");
        jedis.del(channel);//实时消息系统
    }
}
class SubClient {
    private Jedis jedis;//
    public SubClient(String host,int port){
        jedis = new Jedis(host,port);
    }
    public void sub(JedisPubSub listener,String channel){
        jedis.subscribe(listener, channel);
        //此处将会阻塞，在client代码级别为JedisPubSub在处理消息时，将会“独占”链接
        //并且采取了while循环的⽅方式，侦听订阅的消息
    }
}
public class PubSubTest {
    /**
    * @param args
    */
    static String host = "127.0.0.1";
    static int port = 10011;
    public static void main(String[] args) throws Exception{
    PubClient pubClient = new PubClient(host,port);
    final String channel = "pubsub-channel";
    pubClient.pub(channel, "before1");
    pubClient.pub(channel, "before2");
    Thread.sleep(2000);
    //消息订阅者⾮非常特殊，需要独占链接，因此我们需要为它创建新的链接；
    //此外，jedis客户端的实现也保证了“链接独占”的特性，sub⽅方法将⼀一直阻塞，
    //直到调⽤用listener.unsubscribe⽅方法
    Thread subThread = new Thread(new Runnable() {
        @Override
        public void run() {
            try{
                SubClient subClient = new SubClient(host,port);
                System.out.println("----------subscribe operation begin-------");
                JedisPubSub listener = new PrintListener();
                //在API级别，此处为轮询操作，直到unsubscribe调⽤用，才会返回
                subClient.sub(listener, channel);
                System.out.println("----------subscribe operation end-------")
                ;
            }catch(Exception e){
                e.printStackTrace();
            }
        }
    });
    subThread.start();
    int i=0;
    while(i < 10){
        String message = RandomStringUtils.random(6, true, true);//apache-commons
        pubClient.pub(channel, message);
        i++;
        Thread.sleep(1000);
    }
    //被动关闭指示，如果通道中，消息发布者确定通道需要关闭，那么就发送一个“quit”
    //那么在listener.onMessage()中接收到“quit”时，其他订阅client将执行“unsubscribe”操作。
    pubClient.close(channel);
    //此外，你还可以这样取消订阅
    //listener.unsubscribe(channel);
    }
}
```

输出：

```
----------subscribe operation begin-------
message receive:erRIEe,channel:pubsub-channel...2016-03-15 15:53:52
message receive:Ovcwiw,channel:pubsub-channel...2016-03-15 15:53:53
message receive:STPWfV,channel:pubsub-channel...2016-03-15 15:53:54
message receive:SR4iIk,channel:pubsub-channel...2016-03-15 15:53:55
message receive:GI3Ege,channel:pubsub-channel...2016-03-15 15:53:56
message receive:0V1JUt,channel:pubsub-channel...2016-03-15 15:53:57
message receive:3iU8BV,channel:pubsub-channel...2016-03-15 15:53:58
message receive:BqeI2x,channel:pubsub-channel...2016-03-15 15:53:59
message receive:D53cHF,channel:pubsub-channel...2016-03-15 15:54:00
message receive:quit,channel:pubsub-channel...2016-03-15 15:54:01
----------subscribe operation end-------
```
