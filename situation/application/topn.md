## 排行榜取TOP N

{{indexmenu_n>1}}

游戏服务器中涉及到很多排行信息，取TOP
N操作，比如玩家等级排名、金钱排名、战斗力排名等，虽然通过关系数据库也可以实现，但是随着数据量增多，数据库的排序压力就会变大。

这些操作对于Redis来说都很简单，即使你有几百万个用户，每分钟都会有几百万个新的得分。

只需要jedis.zrevrank(key,member);

``` java
import java.util.ArrayList;
import java.util.List;
import java.util.Set;
import java.util.concurrent.ThreadLocalRandom;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Tuple;
public class HelloWorldApp {
    static int TOTAL_SIZE = 10000;
    //获取长度为8，由小写字母组成的随机名称
    public static String randomName(int length) {
        StringBuilder builder = new StringBuilder(length);
        for (int i = 0; i < length; i++) {
            builder.append((char) ThreadLocalRandom.current().nextInt(97,122));//a~z
        }
        return builder.toString();
    }
    public static void main(String[] args)
    {
        //连接信息，从控制台可以获得
        String host = "127.0.0.1";
        int port = 10011;
        Jedis jedis = new Jedis(host, port);
        //排行榜应用，取TOP N操作
        try {
            //Key(键)
            String key = "游戏排行榜！";
            //清除可能的已有数据
            jedis.del(key);
            //模拟生成若干个游戏选手
            List<String> playerList = new ArrayList<String>();
            for (int i = 0; i < TOTAL_SIZE; ++i)
            {
                //随机生成每个选手的名称
                playerList.add(randomName(8));
            }
            System.out.println("输入全部" + TOTAL_SIZE +" 选手 ");
            //记录每个选手的得分
            for (int i = 0; i < playerList.size(); i++)
            {
                //随机生成数字，模拟选手的游戏得分
                int score = (int)(Math.random()*5000);
                String member = playerList.get(i);
                if (i < 10) {
                    System.out.println("选手名称：" + member + "， 选手得分: " + score
                    );
                }
                //将选手的名称和得分，都加到对应key的SortedSet中去
                jedis.zadd(key, score, member);
            }
            System.out.println("更多选手得分......");
            //从对应key的SortedSet中获取已经排好序的选手列表
            Set<Tuple> scoreList = jedis.zrevrangeWithScores(key, 0, -1);
            //输出打印Top100选手排⾏行榜
            System.out.println();
            System.out.println(" "+key);
            System.out.println(" Top 100 选手");
            scoreList = jedis.zrevrangeWithScores(key, 0, 99);
            for (Tuple item : scoreList) {
                System.out.println("选手名称："+item.getElement()+"， 选手得分:"+Doubl
                e.valueOf(item.getScore()).intValue());
            }
            //输出某个选手的排名
            String player = playerList.get(0);
            System.out.println();
            System.out.println(" "+key);
            System.out.println(" 选手"+player+"的排名: "+ jedis.zrevrank(key,player));
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            jedis.quit();
            jedis.close();
        }
    }
}
```

输出：

```
输入全部10000 选手
选手名称：hegmsrcs， 选手得分: 1191
选手名称：ocvhhxke， 选手得分: 653
选手名称：ekgdllwj， 选手得分: 694
选手名称：kxwsnnjj， 选手得分: 2051
选手名称：vjflcktn， 选手得分: 2100
选手名称：jtrlmnlk， 选手得分: 4257
选手名称：aatbchgk， 选手得分: 2912
选手名称：phukvvxy， 选手得分: 2044
选手名称：aqqdqnel， 选手得分: 1859
选手名称：hyndvsen， 选手得分: 2381
更多选手得分......
游戏排行榜！
Top 100 选手
选手名称：kqyhxehe， 选手得分:4999
选手名称：datnveli， 选手得分:4998
选手名称：ovxfislm， 选手得分:4997
选手名称：llqnigun， 选手得分:4997
选手名称：ckikmasa， 选手得分:4997
选手名称：wlmdrpnx， 选手得分:4996
选手名称：trslhgga， 选手得分:4996
选手名称：bkbfnutg， 选手得分:4996
选手名称：yqesafda， 选手得分:4995
选手名称：grtjbjrq， 选手得分:4995
更多选手排名......
游戏排行榜！
选手hegmsrcs的排名: 7618
```
