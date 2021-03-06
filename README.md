# Java parse ini file
可以解析带section的INI文件， 如果不带section，则 section 名默认为 “default”；

一个节可以扩展或者通过在节的名称之后带一个冒号(:)来继承目标节的数据，如果被继承的节点key值重复，则覆盖被继承者的数据；具体可以看下面的例子。

## INI文件(app.ini) Exp：
``` ini
[app]
#生产环境
env = prod

# server 配置
server.host = 127.0.0.1
server.port = 8080

# db 配置
; 驱动
db.driver = "com.mysql.jdbc.Driver"
; 数据连接
db.url = "jdbc:mysql://localhost:3306/test";
; 用户名
db.user = "root"
; 密码
db.password = "123456"

# sever1继承app配置
[server1:app]
env = dev

server.port = 8081
; 数据连接
db.url = "jdbc:mysql://localhost:3307/server1";
; 用户名
db.user = "server"
; 密码
db.password = "123456"

# sever2继承app配置
[server2:server1]
env = qa
server.port = 8082
```

Java使用代码

``` java
package com.vcqr;

import java.io.IOException;
import java.util.HashMap;

public class App
{

    public static void main(String[] args)
    {
        String fileName = "app.ini";
        ParseIni config;
        try {
            config = new ParseIni(fileName);

            // 获取app的当前环境
            Object app = config.get("env", "app");
            System.out.println("app.env = " + app);

            // 获取server1的当前环境
            Object s1Env = config.get("env", "server1");
            System.out.println("server1.env = " + s1Env);

            // 获取server2的当前环境
            Object s2Env = config.get("env", "server2");
            System.out.println("server2.env = " + s2Env);

            // 获取server1的当前server配置
            Object s1Sever = config.get("server.port", "server1");
            System.out.println("server1.server.port = " + s1Sever);

            // 获取server2的当前server环境
            Object s2Sever = config.get("server.port", "server2");
            System.out.println("server2.serer.port = " + s2Sever);
            
            System.out.println("server2.server = " + config.get("server", "server2"));
            System.out.println("server2.db = " + config.get("db", "server2"));

            // 设置节 sever3
            config.setSection("server3");
            HashMap<String, Object> node1 = config.getSection("server3");

            // 给节增加节点
            node1.put("env", "local");
            System.out.println("server3.env = " + config.get("env", "server3"));

        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}

```
### 代码输出
``` txt
app.env = prod
server1.env = dev
server2.env = qa
server1.server.port = 8081
server2.serer.port = 8082
server2.server = {port=8082, host=127.0.0.1}
server2.db = {"password":"123456","driver":"com.mysql.jdbc.Driver","user":"server","url":"jdbc:mysql://localhost:3307/server1\";"}
server3.env = local
```

如果在使用过程遇到问题，或者发现bug，或者有更好的建议可以发邮件给我！ 欢迎沟通交流！
