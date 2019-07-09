## 构建队列系统

{{indexmenu_n>3}}

通过使用list的lpop及lpush命令进行队列的写入和消费，可以轻松实现消息队列；由于它是独立于游戏服务器的，所以多个游戏服务器可以通过它来交换数据、发送事件，通过使用sorted
set，甚至可以构建有优先级的队列系统。

``` java
import redis.clients.jedis.Jedis;
class ProducerThread extends Thread {
    private Jedis jedis;
    private int port;
    private String host,key;
    ProducerThread(String host, int port, String key) {
        super(host);
        this.key = key;
        this.port = port;
        jedis = new Jedis(host, port);
    }
    protected void finalize( ){
        jedis.close();
    }
    public void run() {
        //System.out.println("ProducerThread is running...");
        for(int i = 0; i < 10; i++) {
            jedis.lpush(key, "job"+ Integer.toString(i));
            System.out.println("ProducerThread push job"+ Integer.toString(i));
        }
    }
}
class ConsumerThread extends Thread {
    private Jedis jedis;
    private int port;
    private String host,key;
    ConsumerThread(String host, int port, String key) {
        super(host);
        this.key = key;
        this.port = port;
        jedis = new Jedis(host, port);
    }
    protected void finalize( ) {
        jedis.close();
    }
    public void run() {
        //构建队列系统
        //System.out.println("ConsumerThread is running...");
        for(int i = 0; i < 10; i++) {
            System.out.println("ConsumerThread pop "+ jedis.lpop(key));
        }
    }
}
public class QueueSystem {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 10011;
        String key = "jobs";
        ProducerThread pT = new ProducerThread(host,port,key);
        ConsumerThread cT = new ConsumerThread(host,port,key);
        pT.start();
        cT.start();
    }
}
```

输出:

```
ConsumerThread pop null
ProducerThread push job0
ConsumerThread pop job0
ProducerThread push job1
ConsumerThread pop job1
ProducerThread push job2
ConsumerThread pop job2
ProducerThread push job3
ConsumerThread pop job3
ProducerThread push job4
ConsumerThread pop job4
ProducerThread push job5
ConsumerThread pop job5
ProducerThread push job6
ConsumerThread pop job6
ProducerThread push job7
ConsumerThread pop job7
ProducerThread push job8
ConsumerThread pop job8
ProducerThread push job9
```
