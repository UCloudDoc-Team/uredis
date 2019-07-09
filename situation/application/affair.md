{{indexmenu_n>7}}

## 事务处理

用户可以使用MULTI,EXEC,DISCARD,WATCH,UNWATCH指令用来执行原子性的事务操作。

``` java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;
import java.util.List;
public class JTransaction {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 10011;
        Jedis jedis1 = new Jedis(host, port);
        Jedis jedis2 = new Jedis(host, port);
        String key = "transaction-key";
        jedis1.set(key, "20");
        //jedis1 watch key
        jedis1.watch(key);//如果在执⾏行事务之前，其他的客户端改变了key，则事务执⾏行失败。
        Transaction tx = jedis1.multi();//开始事务
        tx.get(key);
        tx.get(key);
        tx.get(key);
        //jedis2.incr(key);//如果jedis2改变key，那么jedis1的事务就会失败
        List<Object> result = tx.exec();//执⾏行事务
        if(result == null || result.isEmpty()){
            System.out.println("Transaction error...");
            return;
        }
        for(Object rt : result){
            System.out.println(rt.toString());
        }
    }
}
```

输出：

```
20
20
20
```
