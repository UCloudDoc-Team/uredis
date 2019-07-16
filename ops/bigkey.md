# 主备Redis搜索大key

{{indexmenu_n>1}}

## 背景信息

Redis 提供了 list、hash、zset 等复杂类型的数据结构，业务在使用的时候可能由于 key 设计不合理导致某个 key
过大，因此我们需要搜索工具来发现过大的key。

## 操作步骤

1、创建以下扫描脚本\`scan\_big\_key.py\`

``` python

#!/usr/bin/python2
import sys
import os
import redis
import time
import datetime
 
BigNum=10000
string_keys=[]
hash_keys=[]
list_keys=[]
set_keys=[]
zset_keys=[]
def scan_hash(source):
    #print "Begin Import Hash Type"
    keys_count = len(hash_keys)
    #print "Hash Key Count is:",keys_count
    for key in hash_keys:
        hlen=source.hlen(key)
        if hlen > BigNum :
            print "Hash key:%s, size:%d" % (key, hlen)
 
def scan_set(source):
    #print "Begin Import Set Type"
    keys_count = len(set_keys)
    #print "Set Key Count is:",keys_count
    for key in set_keys:
        slen=source.scard(key)
        if slen > BigNum :
            print "Set key:%s, size:%d" % (key, slen)
 
def scan_zset(source):
    #print "Begin Import ZSet Type"
    keys_count = len(zset_keys)
    #print "ZSet Key Count is:",keys_count
    for key in zset_keys:
        zlen = source.zcard(key)
        if zlen > BigNum :
            print "ZSet key:%s, size:%d" % (key, zlen)
 
def scan_list(source):
    #print "Begin Import List Type"
    keys_count = len(list_keys)
    #print "List Key Count is:",keys_count
    for key in list_keys:
        llen= source.llen(key)
        if llen > BigNum :
            print "Hash key:%s, size:%d" % (key, llen)
            
def scan_string(source):
    #print "Begin Import List Type"
    keys_count = len(string_keys)
    #print "List Key Count is:",keys_count
    for key in string_keys:
        slen= source.strlen(key)
        if slen > BigNum :
            print "String key:%s, size:%d" % (key, slen)
 
def read_type_keys(source, ScanIndex):
    ScanIndex, keys = source.execute_command('scan', ScanIndex, "count",200000)
    keys_count = len(keys)
    #print "Key Count is:",keys_count
    pipe = source.pipeline(transaction=False)
    #for key in keys:
    index=0
    pipe_size=5000
    while index < keys_count:
        old_index=index
        num=0
        while (index < keys_count) and (num < pipe_size):
            pipe.type(keys[index])
            index +=1
            num +=1
        results=pipe.execute()
        for type in results:
            if type == "string":
                string_keys.append(keys[old_index])
            elif type == "list":
                list_keys.append(keys[old_index])
            elif type == "hash":
                hash_keys.append(keys[old_index])
            elif type == "set":
                set_keys.append(keys[old_index])
            elif type == "zset":
                zset_keys.append(keys[old_index])
            else :
                print "no key"
                #print keys[old_index]," is not find when TYPE"
            old_index +=1
    return ScanIndex
 
if __name__=='__main__':
    argc = len(sys.argv)
    if argc < 3:
        print "usage: %s sourceIP sourcePort [password]" % (sys.argv[0])
        exit(1)
    SrcIP = sys.argv[1]
    SrcPort = int(sys.argv[2])
    Password = ""
    if argc == 4:
        Password = sys.argv[3]
 
    start=datetime.datetime.now()
    source=redis.Redis(host=SrcIP,port=SrcPort,password=Password)
 
    index=0
    times=1
    strNum=0
    setNum=0
    zsetNum=0
    listNum=0
    hashNum=0
    print "begin scan key"
    while index != '0' or times == 1:
        first = False
        print "Times: ", times
        times += 1
        index=read_type_keys(source, index)
        strNum += len(string_keys)
        setNum += len(set_keys)
        zsetNum += len(zset_keys)
        listNum += len(list_keys)
        hashNum += len(hash_keys)
 
        scan_hash(source)
        scan_list(source)
        scan_set(source)
        scan_zset(source)
        scan_string(source)
        string_keys=[]
        hash_keys=[]
        list_keys=[]
        set_keys=[]
        zset_keys=[]
 
    print "String Key Count is: ", strNum
    print "Set Key Count is :   ", setNum
    print "ZSet Key Count is:   ", zsetNum
    print "List Key Count is:   ",listNum
    print "Hash Key Count is:   ",hashNum
 
    stop=datetime.datetime.now()
    diff=stop-start
    print "Finish, token time:",str(diff)

```

2、执行脚本

`./scan_big_key.py sourceIP sourcePort [password]`
