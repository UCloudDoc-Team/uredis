## 取最新N条评论



如果要取网站的最新评论

``` java
import java.util.List;
import java.util.UUID;
import redis.clients.jedis.Jedis;
public class LastestN {
    static int TOTAL_NUM = 5000;
    static String host = "127.0.0.1";
    static int port = 10011;
    static String key = "comments";
    static Jedis jedis;
    public static void getLastestComments(int start, int num) {
        System.out.println("最新20条评论:");
        List<String> lastest_list = jedis.lrange(key, start, start + num - 1);
        if (lastest_list.size() < num) {
            //get remaining comments from db
        }
        for (int i = 0; i < lastest_list.size(); i ++) {
            System.out.println("CommentID " + Integer.toString(i) + " "+lastest_
            list.get(i));
        }
    }
    public static void setComments() {
        System.out.println(key);
        for (int i = 0; i < TOTAL_NUM; i++) {
            String commentID = UUID.randomUUID().toString();
            jedis.lpush(key, commentID);
            jedis.ltrim(key, 0, 99);//永远最多保存100条评论在redis中。
            if (i < 10) {
                System.out.println("CommentID: " + commentID);
            }
        }
        System.out.println("更多CommentID......");
        System.out.println("");
    }
    public static void main(String[] args) {
        jedis = new Jedis(host, port);
        //添加若干条评论；
        setComments();
        //取最新20条评论
        getLastestComments(0,20);
        jedis.close();
    }
}
```

输出：

```
comments
CommentID: b93db3f4-ff88-422a-ad49-bcbf7de5a396
CommentID: 3bf56f8a-5d90-4fa1-a068-7dd7c3993917
CommentID: 83a32ebd-89c4-40a5-bbd8-ace6bf723c57
CommentID: 6003d965-f6cd-4e60-8b12-f9494fcb9bc0
CommentID: a932c934-5dfd-4a5f-90da-5a40da468e78
CommentID: 08ce995b-2ee1-4db9-8e3c-ca5069f87cce
CommentID: ce5b57d5-fc02-4c91-90d5-bbe211073e0b
CommentID: 5314a796-5574-4282-aab5-d8a8fc6e7ade
CommentID: 3018252d-4f9b-40e7-bbbd-e0f3ab8b753d
CommentID: 730bc8f5-d5fc-4d29-adf8-23c2fb632c0b
更多CommentID......
最新20条评论:
CommentID 0 d194a83c-005a-411e-ba36-2b513c3565c1
CommentID 1 c8104907-8912-463a-9d05-7fe0385d13d9
CommentID 2 88b918ac-bf35-4687-a06c-af5e6159a376
CommentID 3 324ff8c1-dfa5-46b3-9463-8a2d6aba3d26
CommentID 4 6b3b76b0-3ce6-4dd4-9ed9-b40618330a44
CommentID 5 7561efe9-96e0-46df-8f7c-e5f6812246c1
CommentID 6 937122ca-a203-4ae9-89d6-454dbf616e1e
CommentID 7 8e0f24fe-152a-4297-afbd-703c51fee50e
CommentID 8 328f4858-6adc-41c7-88c1-a896c5b122a5
CommentID 9 50151b7e-1225-4093-a30b-9d370b44ea25
CommentID 10 dd1bd655-760e-41af-8929-9986dce9a41b
CommentID 11 358fa7d3-4291-4c1b-8b2e-dbee55c60084
CommentID 12 7367b111-9144-428b-af32-685004318c97
CommentID 13 5e06f20a-a5b0-4e85-98c4-9be4ec613d70
CommentID 14 924ac607-8af2-47b7-a08a-37ee851a1693
CommentID 15 add23360-f2d9-4049-b935-97a8823176e4
CommentID 16 15aca269-e4dd-4f2f-9eaa-cf8dc19cc8f3
CommentID 17 45c6f96b-4a8a-4b57-a1f4-f10621911d67
CommentID 18 c0a4fcd1-8df5-4f44-9a04-ba23ce8c8168
CommentID 19 3e06fc46-766a-4e78-a67b-276840f72f62
```
