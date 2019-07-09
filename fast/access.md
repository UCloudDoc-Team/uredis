\# 访问实例

{{indexmenu_n>2}}

出于安全性考虑，云内存Redis实例仅能在内网进行访问，以下以Redis协议为例简述如何访问云内存存储。

\#\# telnet访问

```
[test@u205 ~]$ telnet 127.0.0.1 6379
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
set key value
+OK
get key
$5
value
del key
:1
```

\#\# php访问

``` php
<?php
    $redis = new Redis();
    $redis->connect('10.4.7.17', 6379);
    $key = "testkey";
    $tvalue = "testvalue";
    $redis->set($key, $tvalue);
    $nvalue = $redis->get($key);
    print_r($nvalue . "\n");
?>
```

\#\# Python访问

``` python
#!/usr/bin/env python2
# coding=utf8

import sys, os
import redis

if __name__ == "__main__":

    ip = '10.4.7.17'
    port = 6379

    r = redis.StrictRedis(ip, port, 0)

    key = 'testkey'
    tvalue = "testvalue"
    r.set(key, tvalue)
    nvalue = r.get(key)
    print nvalue
```
