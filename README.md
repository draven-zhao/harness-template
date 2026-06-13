```mermaid
graph TD
    %% 客户端层
    A[客户端<br/>Web/移动端/CLI] -->|HTTP/HTTPS 请求| B[Go API 服务入口]
    
    %% 接入与认证层
    B --> C[认证中间件<br/>JWT 校验/Token 刷新]
    C -->|未登录/Token失效| D[登录接口<br/>账号密码校验/Bcrypt 加密]
    D -->|校验通过| E[生成JWT Token 返回]
    C -->|Token 有效| F[路由分发]
    
    %% 业务逻辑层
    F --> G[数据库架构查询服务]
    G --> H[缓存判断逻辑<br/>Key: 查询维度(库名/表名/字段名)]
    
    %% 缓存层
    H -->|命中缓存| I[Redis 缓存<br/>存储热点库表结构/高频查询元数据<br/>设置过期时间+缓存击穿保护]
    H -->|未命中缓存| J[MySQL 查询执行器]
    
    %% 数据存储层
    J --> K[MySQL 数据库<br/>查询 information_schema 系统库<br/>获取库/表/字段/索引等架构元数据]
    K -->|返回结果| L[结果封装]
    L -->|回种缓存| I
    L -->|返回响应| A
