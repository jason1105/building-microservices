## 第九章 安全
### 9.1 身份验证和安全
验证主体：principal

分布式系统中使用的验证方式：单点登录SSO （Single Sign-On）

1. 常见的SSO：SAML和OpenID Connect
    
    客户端请求服务A → 服务A请求身份提供者进行验证 → 验证通过后继续业务
    
    身份提供者通常会连接目录服务，例如LDAP或者Active Directory。通常情况下身份提供者和目录服务在一起。
    
    OpenID Connect是OAuth 2.0的一个标准，使用REST。而SAML使用SOAP。
    
    OpenID Connect的身份提供者比较少，OpenAM和Gluu是选择之一。
    
    笔者认为OpenID Connect会更流行

    问题：每个服务都要自己去和身份提供者握手。

2. 单点登录网关：使用单点登录网关可以统一处理重定向行为，为了下游可以接收主体信息，对于HTTP而言可以将主体信息存放在HTTP头中。Shibboleth工具可以做到这一点，并且和SAML可以很好集成。
    
    缺点：在开发过程中，需要启动网关进行调试。网关可能成为耦合点。
    
3. 授权粒度：角色与组织匹配，不要和业务匹配。

### 9.2 服务间的身份验证和授权

1. 默认可信：隐式信任模型
2. HTTP(S)基本验证：HTTP验证不能保证数据安全，所以使用HTTPS，但HTTPS证书需要更多的维护，并且SSL不能被反向代理缓存。
3. 使用SAML或者OpenID Connect：为客户端创建账户（服务账户），问题是客户端需要保存用户和密码，这并不安全。    
4. 客户端证书：使用TLS（安全传输层协议），同样有证书管理的问题。
5. HTTP上的HMAC（Hash-based Message Authentication Code，基于哈希的消息码）：AWS的S3和JWT使用了这个方法。
6. API密钥：针对公共API，使用API密钥，可以识别调用者。相关工具可以提供限速、变现、API目录和发现系统等功能。

    ![](http://m.qpic.cn/psb?/V120cGPg1BkF5W/vZEDr4re*xlcnQuvdrvytAdNq07TMUKiDk6UlnhEmX0!/b/dNEAAAAAAAAA&bo=rgMWAq4DFgIBByA!&rf=viewer_4)

7. 代理问题：下游服务对上游代理的请求的信任如何解决，或者由下游服务自己解决？

### 9.3 静态数据的安全

1. 使用众所周知的算法：对于静态数据，使用AES这样的，在Java和.Net上都有实现。关于密码可以使用加盐密码哈希技术。
2. 关于密钥，使用单独的设备、或者使用单独的密钥库。
3. 适当选择需要加密的数据。
4. 需要的时候解密。
5. 备份数据也要加密。

### 9.4 深度防御

以下是原则：
1. 防火墙：多多益善，外部防火墙控制一般访问，例如特定端口特定类型的访问，限定特定IP范围的连接数量。主机防火墙则确保主机安全。
2. 日志：多多益善，但不要把敏感信息泄露到日志中。
3. 入侵检测（和预防）系统：IDS、IPS不仅仅对外，也可以对内寻找可以行为，这与防火墙不同。
4. 网络隔离：通过VPN等技术进行隔离
5. 操作系统：给用户最少的权限，定期自动打补丁，微软的SCCM，红帽的Spacewalk。Ansible、Puppet或Chef也可以。
   关注操作系统本身的安全模块，例如AppArmour。ubuntu、SuSE默认用AppArmour，红帽用SELinux。

### 9.5 一个示例
![](http://m.qpic.cn/psb?/V120cGPg1BkF5W/Wm0TzrGySwg4agr9T7JxMnstN9PFcJpqouHHMWhMkzs!/b/dDEBAAAAAAAA&bo=pQM7AgAAAAADB70!&rf=viewer_4)

### 9.6 保持节俭
不保存冗余的数据。

### 9.7 人的因素
来自（曾经是）组织内部人员的破坏

### 9.8 黄金法则
不要使用自己创造的加密算法，不要创造加密协议。

### 9.9 内建安全
熟悉OWASP十大列表。在CI中使用静态分析工具。

### 9.10 外部验证
不识庐山真面目，只缘身在此山中。寻求外部力量。

### 9.11 总结
1. 不要创造加密算法
2. OWASP（开放式WEB应用安全项目）是基于浏览器的一个很好的开始，是必备读物
    
    
    
    
    