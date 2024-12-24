# **Redis SSL 加密连接案例**

## **概述**
Redis 是一个高性能的键值存储数据库，为了保证数据传输的安全性，推荐在通信过程中启用 SSL/TLS 加密。本案例展示如何通过 **Python**、**Go** 和 **Java** 实现 SSL 加密连接到 Redis 服务器。

---

## **Python 示例**

- 使用 Python 3 环境。
- 安装 `redis` 库：
  ```bash
  pip3 install redis
  ```
- 连接代码:
  ``` python
  #!/usr/bin/env python3
  #-*- coding: utf-8 -*- 
  import redis

  # 创建 Redis 连接池
  pool = redis.ConnectionPool(
      host="10.xx.xx.246",          # Redis 服务器地址
      port=6379,                    # Redis 端口
      password="*************",     # Redis 密码
      connection_class=redis.SSLConnection,
      ssl_cert_reqs="required",     # 验证证书
      ssl_ca_certs="ca.crt"         # 指定 CA 证书路径
  )

  # 使用连接池创建 Redis 客户端
  client = redis.Redis(connection_pool=pool)

  # 测试连接池和 SSL
  key = "python3_pool_ssl_connect_test"
  try:
      print("Ping Redis server:", client.ping())  # 测试连接
      client.set(key, "python3")   # 写入数据
      print(client.get(key))  # 读取数据
  except Exception as e:
      print("Error:", e)
  ```

## **Java示例**

- ***ca.jks***文件请开通SSL加密后下载获取
- 连接代码:
  ``` java
  package com.example;

  import java.io.InputStream;
  import java.security.KeyStore;
  import java.security.SecureRandom;
  import javax.net.ssl.SSLContext;
  import javax.net.ssl.SSLSocketFactory;
  import javax.net.ssl.TrustManager;
  import javax.net.ssl.TrustManagerFactory;
  import org.apache.commons.pool2.impl.GenericObjectPoolConfig;
  import redis.clients.jedis.Jedis;
  import redis.clients.jedis.JedisPool;

  public class JedisSSLTest {
      private static SSLSocketFactory createTrustStoreSSLSocketFactory(StringjksFile) throws Exception {
          KeyStore trustStore = KeyStore.getInstance("jks");
          InputStream inputStream = null;
          try {
              // 使用类加载器加载资源文件
              inputStream = JedisSSLTest.class.getClassLoader().getResourceAsStream(jksFile);
              if (inputStream == null) {
                  throw new RuntimeException("TrustStore file not found: " + jksFile);
              }
              trustStore.load(inputStream, null);
          } finally {
              if (inputStream != null) {
                  inputStream.close();
              }
          }

          TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance("PKIX");
          trustManagerFactory.init(trustStore);
          TrustManager[] trustManagers = trustManagerFactory.getTrustManagers();

          SSLContext sslContext = SSLContext.getInstance("TLS");
          sslContext.init(null, trustManagers, new SecureRandom());
          return sslContext.getSocketFactory();
      }

	  public static void tlsRedisConnectTest() throws Exception {
	  	  // 从资源文件夹加载 ca.jks
	  	  final SSLSocketFactory sslSocketFactory = createTrustStoreSSLSocketFactory("ca.jks");

	  	  JedisPool pool = new JedisPool(
	  	  	  new GenericObjectPoolConfig(),
	  	  	  "10.xx.xx.246", // Redis 地址
	  	  	  6379,         // Redis 端口
	  	  	  2000,         // 超时时间（毫秒）
	  	  	  "****************", // Redis 密码
	  	  	  0,            // Redis 数据库索引
	  	  	  true,         // 启用 SSL
	  	  	  sslSocketFactory, // 自定义 SSL 套接字工厂
	  	  	  null,         // 客户端证书（未使用）
	  	  	  null          // 主动握手（未使用）
	  	  );

	  	  try (Jedis jedis = pool.getResource()) {
	  	  	  jedis.set("java_tls_connect_test", "java");
	  	  	  System.out.println(jedis.get("java_tls_connect_test"));
	  	  } finally {
	  	  	  pool.close();
	  	  }
	  }

      public static void main(String[] args) throws Exception {
	  	  tlsRedisConnectTest();
	  }
  }

  ```


## **Go示例**

- 安装 `go-redis` 库
- 连接代码:
  ``` go
  package main

  import (
      "context"
      "time"
      "fmt"
      "io/ioutil"
      "crypto/tls"
      "crypto/x509"
      "github.com/redis/go-redis/v9"
  )

  func main() {
      caCert, err := ioutil.ReadFile("/root/test_tls_client/go_demo/ca.crt")
      if err != nil {
          fmt.Println("Error loading CA certificate:", err)
          return
      }

      caCertPool := x509.NewCertPool()
      caCertPool.AppendCertsFromPEM(caCert)

      tlsConfig := &tls.Config{
          RootCAs:            caCertPool,
          InsecureSkipVerify: true, // Not actually skipping, we check the cert in VerifyPeerCertificate
          VerifyPeerCertificate: func(rawCerts [][]byte, verifiedChains [][]*x509.Certificate) error {
              // Code copy/pasted and adapted from
              // https://github.com/golang/go/blob/81555cb4f3521b53f9de4ce15f64b77cc9df61b9/src/crypto/tls/handshake_client.go#L327-L344, but adapted to skip the hostname verification.
              // See https://github.com/golang/go/issues/21971#issuecomment-412836078.

              // If this is the first handshake on a connection, process and
              // (optionally) verify the server's certificates.
              certs := make([]*x509.Certificate, len(rawCerts))
              for i, asn1Data := range rawCerts {
                  cert, err := x509.ParseCertificate(asn1Data)
                  if err != nil {
                      panic(err)
                  }
                  certs[i] = cert
              }

              opts := x509.VerifyOptions{
                  Roots:         caCertPool,
                  DNSName:       "", // <- skip hostname verification
                  Intermediates: x509.NewCertPool(),
              }

              for i, cert := range certs {
                  if i == 0 {
                      continue
                  }
                  opts.Intermediates.AddCert(cert)
              }
              _, err := certs[0].Verify(opts)
              return err
          },
      }

      rdb := redis.NewClient(&redis.Options{
          // ssl
          Addr:    "10.xx.xx.246:6379",
          Password: "*****************",

          DialTimeout: 5 * time.Second,  // 连接超时时间
          ReadTimeout: 3 * time.Second,  // 读超时时间
          WriteTimeout: 3 * time.Second, // 写超时时间
          PoolSize:    10,               // 连接池大小
          MinIdleConns: 2,               // 最小空闲连接数
          TLSConfig: tlsConfig,
      })

      // 检测连通性
      ctx := context.Background()
      pong, err := rdb.Ping(ctx).Result()
	  if err != nil {
          fmt.Println(err)
          panic(err)
      }
      fmt.Println("Redis connected:", pong)

      key := "go_tls_connect_test"
      err = rdb.Set(ctx, key, "go", 0).Err()
      if err != nil {
          fmt.Println(err)
          panic(err)
      }

      val, err := rdb.Get(ctx, key).Result()
      if err != nil {
          fmt.Println(err)
          panic(err)
      }
      fmt.Printf("%s: %s\n", key, val)
  }
  ```