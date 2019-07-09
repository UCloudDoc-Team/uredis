{{indexmenu_n>8}}

## 基数统计功能

Redis HyperLogLog
实现了基数统计功能，方便统计一组不同元素且数量很大的数据集，且只耗费很小的空间。如统计网站每天访问的独立IP数量；使用PFADD和PFCOUNT，可以轻松实现。

``` java
import redis.clients.jedis.Jedis;
import java.util.Random;
public class HyperLog {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 10011;
        Jedis jedis = new Jedis(host, port);
        String key = "src_ip";
        jedis.del(key);
        //随机生成10000个 ip，代表访问"www.ucloud.cn"的源ip.
        for (int i = 0; i < 10000; i++) {
            int section1 = new Random().nextInt(255);
            int section2 = new Random().nextInt(253);
            String ip = "10.10." + Integer.toString(section1) + "." + Integer.toSt
            ring(section2);
            //System.out.println(ip);
            jedis.pfadd(key, ip);127.0.0.1
        }
    System.out.println("今天访问www.ucloud.cn的独立ip数为:"+jedis.pfcount(key));
    jedis.close();
    }
}
```

输出：

```
今天访问www.ucloud.cn的独立ip数为:9218
```
