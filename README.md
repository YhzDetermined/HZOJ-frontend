# OJ判题系统后端项目
主要在原知识星球上的OJ判题系统单体项目的基础上进行了功能扩展
## 项目其他部分
项目后端部分 链接： [oj判题系统前端](https://github.com/YhzDetermined/HZOJ-frontend)

项目代码沙箱部分 链接：[oj代码沙箱](https://github.com/YhzDetermined/HZOJ-sandbox)
## 项目主要技术栈
>Java, SpringBoot，MyBatisPlus，MySQL，Docker，RabbitMQ
## 项目页面概览

### 主页/浏览题目页面
![主页/题目页面](image/主页.png)

### 做题页面
![做题页面](image/做题页面.png)

### 创建题目页面
![创建页面](image/创建题目.png)

### 题目管理页面
在项目原有页面的基础上
![题目管理](image/题目管理.png)

### 提交信息页面
在原有项目的基础上，对提交信息的呈现进行了优化，目前支持五种类型的判题结果：
Accept / Wrong answer / Time Limit Exceeded / Out of Memory / Runtime Error
![题目提交](image/提交信息.png)

### 提交详情页面
新开发页面，能够根据用户的权限查看用户的提交代码及判题信息（用户只能查看自己提交的代码，管理员能够查看所有的提交代码。

如果用户提交的代码出现编译报错，可以显示编译报错的信息。
![提交详情](image/查看提交详情.png)

### 登录页面
![登录](image/登录界面.png)

### 注册界面
新开发页面
![登录](image/注册界面.png)
## 项目说明
- Spring Boot 2.7.x
- Spring MVC
- MyBatis + MyBatis Plus 数据访问（开启分页）


### 数据存储

- MySQL 数据库
- Redis 内存数据库

## 主要扩展功能
1. 做了后端判题策略类的修改。根据代码沙箱返回的相应对用户提交程序进行判题。使其能正确捕获编译错误、时间超限、内存超限、运行错误等问题
2. 查看用户提交详情的接口开发和权限判断。用户只能看见自己提交的代码，管理员可以看到所有代码

## 修改配置


### MySQL 数据库配置

1）修改 `application.yml` 的数据库配置为你自己的：

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/my_db
    username: root
    password: 123456
```

2）启动项目，访问 `http://localhost:8101/api/doc.html` 即可打开接口文档，不需要写前端就能在线调试接口了~

![](doc/swagger.png)

### Redis 分布式登录配置

1）修改 `application.yml` 的 Redis 配置为你自己的：

```yml
spring:
  redis:
    database: 1
    host: localhost
    port: 6379
    timeout: 5000
    password: 123456
```

2）修改 `application.yml` 中的 session 存储方式：

```yml
spring:
  session:
    store-type: redis
```
