## 存储社交关系



譬如将用戶的好友/粉丝/关注，可以存在一个set中，这样求两个人的共同好友的操作，只需要用sinter交集命令即可；也可以进行并集，差集操作。

``` java
import java.util.Set;
import redis.clients.jedis.Jedis;
public class CommonFriends {
    public static void main(String[] args) {
        String host = "127.0.0.1";
        int port = 10011;
        Jedis jedis = new Jedis(host, port);
        //my friends
        jedis.sadd("myfriends", "John");
        jedis.sadd("myfriends", "Emliy");
        jedis.sadd("myfriends", "Ben");
        jedis.sadd("myfriends", "Steven");
        System.out.println("my friends are: ");
        Set<String> myList = jedis.smembers("myfriends");
        for (String item:myList) {
            System.out.print(item+" ");
        }
        //your friends
        jedis.sadd("yourfriends", "Mark");
        jedis.sadd("yourfriends", "Tim");
        jedis.sadd("yourfriends", "Willim");
        jedis.sadd("yourfriends", "Ben");
        jedis.sadd("yourfriends", "Steven");
        System.out.println("\n");
        System.out.println("your friends are: ");
        Set<String> yourList = jedis.smembers("yourfriends");
        for (String item:yourList) {
            System.out.print(item+" ");
        }
        //our common friends
        System.out.println("\n");
        System.out.println("our common friends are: ");
        Set<String> commonList = jedis.sinter("myfriends","yourfriends");
        for (String item:commonList) {
            System.out.print(item+" ");
        }
        jedis.close();
    }
}
```

输出：

```
my friends are:
Steven Emliy John Ben
your friends are:
Steven Mark Tim Ben Willim
our common friends are:
Steven Ben
```
