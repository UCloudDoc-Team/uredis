## 计数，生成唯一ID

{{indexmenu_n>2}}

Redis的命令都是原子性的，可以轻松地利用INCR，DECR命令来构建计数器系统。同理，可以用INCR命令，为游戏玩家生成唯一的ID。

示例：

``` java
import java.util.Scanner;
import redis.clients.jedis.Jedis;
public class Incr {
    // 访问一次web,计数一次
    public static void accessWeb(Jedis jedis, String url) {
        jedis.incr(url);
    }
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 10011;
        Jedis jedis = new Jedis(host, port);
        String url = "www.ucloud.cn";
        //获取原始的值
        long origin_cnt = Long.parseLong(jedis.get(url));
        //接收终端输⼊入
        Scanner sc = new Scanner(System.in);
        while(true) {
            System.out.println("访问 " + url +" ? [y/n]");
            String ac = sc.nextLine();
            if (ac.equals("y")) {
            accessWeb(jedis,url);
            }else {
                break;
            }
        }
        sc.close();
        //获取现在的值
        long now_cnt = Long.parseLong(jedis.get(url));
        //计算访问www.ucloud.cn的次数。
        System.out.println("你总共访问了 "+ url+ " " + Long.toString(now_cnt - o
        rigin_cnt)+"次.");
        jedis.close();
    }
}
```

输出

```
访问 www.ucloud.cn ? [y/n]
y
访问 www.ucloud.cn ? [y/n]
y
访问 www.ucloud.cn ? [y/n]
y
访问 www.ucloud.cn ? [y/n]
y
访问 www.ucloud.cn ? [y/n]
n
你总共访问了 www.ucloud.cn 4次.
```
