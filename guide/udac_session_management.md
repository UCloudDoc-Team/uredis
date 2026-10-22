# 会话管理

数据库自治中心 UDAC 提供 Redis 会话管理功能，可查看实例会话信息，并按 Redis 形态对会话或连接进行管理。

## 功能入口

进入数据库自治中心 UDAC 的**实例管理**页面，选择 **Redis**，在目标 Redis 实例的操作菜单中点击**会话管理**。

![Redis 实例操作菜单中的会话管理入口](https://cdnv2-cache.udelivrs.com/2026/10/3edf1cec3f5454d493f29a9a158cad74_1792641397904.png?iopcmd=convert&dst=webp)

进入会话管理页面后，可查看实例会话列表。页面支持手动刷新，也可通过自动刷新开关更新会话数据。

![主备版 Redis 会话管理页面](https://cdnv2-cache.udelivrs.com/2026/10/d64120fa565d76fa1c79ebdee79027fa_1792641416218.png?iopcmd=convert&dst=webp)

## 主备版 Redis

主备版 Redis 支持终止指定会话。选择目标会话后，可使用批量终止操作；终止前会展示待关闭的客户端会话，确认后执行。

![批量终止 Redis 会话确认窗口](https://cdnv2-cache.udelivrs.com/2026/10/6159760ff4d38e786f489b2456b4ff57_1792641433305.png?iopcmd=convert&dst=webp)

> **注意：** 终止会话会断开对应客户端连接，可能影响正在进行的业务请求。请确认目标会话后再执行。

## 分布式版 Redis

分布式版 Redis 的会话管理页面区分**分片**和**代理**，可分别查看和管理：

- **分片：** 可查看分片实例会话，并终止指定会话。
- **代理：** 可查看代理连接，并关闭指定连接。

![分布式 Redis 会话管理中的分片和代理视图](https://cdnv2-cache.udelivrs.com/2026/10/c95ffa797eee1128db93220141d4680c_1792641456050.png?iopcmd=convert&dst=webp)

执行终止或关闭操作前，请核对当前选择的分片、代理及目标会话或连接，避免影响其他客户端。
