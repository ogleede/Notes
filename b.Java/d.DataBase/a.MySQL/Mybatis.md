## 3.CURD

### 1.namespace

namespace 中的包名要和mapper接口的包名一致

### 2.select

* id:对应的namespace中的方法名；
* resultType:Sql语句执行的返回值
* parameterType:参数类型

编写接口->编写对应的mapper中的sql语句->测试

### 3.insert

### 4.update

### 5.delete

### 6.万能的Map

用map来传，不必知道这个数据库里面有什么 ，只需要写需要的字段



假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用map

map传递参数，直接在sql中取出key即可！

对象传递参数，直接在sql中取对象的属性即可！

只有一个基本类型参数的情况下，可以直接在sql中取到！

多个参数用map，或者注解

### 7.模糊查询

1.Java代码执行的时候，传递通配符% %

```java
List<User> userList = mapper,getUserLike("%李%");
```



2.在sql拼接中使用通配符

```sql
select * from mybatis.user where name like "%" #{value} "%";
```



## 4.配置解析

### 4.1 核心配置文件

* mybatis-config.xml

```xml
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```







## 多对一

对学生来说：多个学生 **关联** 一个老师，多对一

对老师来说：**集合**  一个老师有很多学生，一对多

