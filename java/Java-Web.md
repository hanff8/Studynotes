##  使用hsql时出现user lacks privilege or object not found:

注意初始化hibernate的参数,`hbm2ddl` 不要拼错了
```shell
props.setProperty("hibernate.hbm2ddl.auto", "update");
```

## forward 与 redirect的关系

forward 即`转发` 指的是服务器内部的转发，浏览器不可见 
```ascii
                          ┌────────────────────────┐
                          │      ┌───────────────┐ │
                          │ ────>│ForwardServlet │ │
┌───────┐  GET /morning   │      └───────────────┘ │
│Browser│ ──────────────> │              │         │
│       │ <────────────── │              ▼         │
└───────┘    200 <html>   │      ┌───────────────┐ │
                          │ <────│ HelloServlet  │ │
                          │      └───────────────┘ │
                          │       Web Server       │
                          └────────────────────────┘
```
redirect 即`重定向` 它会立刻根据`Location`的指示发送一个新的`GET /hello`请求，这个过程就是重定向
```ascii
┌───────┐   GET /hi     ┌───────────────┐
│Browser│ ────────────> │RedirectServlet│
│       │ <──────────── │               │
└───────┘   302         └───────────────┘


┌───────┐  GET /hello   ┌───────────────┐
│Browser│ ────────────> │ HelloServlet  │
│       │ <──────────── │               │
└───────┘   200 <html>  └───────────────┘
```

转发和重定向的区别在于，转发是在Web服务器内部完成的，对浏览器来说，它只发出了一个HTTP请求

## 使用mvn打包时候出现 ` Fatal error compiling: 错误: 无效的目标发行版：17 -> [Help 1]`
1. 检查java.version是否对应
2. 检查JAVA_HOME 环境变量
3. 可以使用`mvn --version`来查看maven使用的jdk版本