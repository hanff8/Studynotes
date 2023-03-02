##  使用hsql时出现user lacks privilege or object not found:

注意初始化hibernate的参数,`hbm2ddl` 不要拼错了
```shell
props.setProperty("hibernate.hbm2ddl.auto", "update");
```
