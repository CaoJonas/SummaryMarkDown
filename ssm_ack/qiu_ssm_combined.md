##### ssm 处理 时间类型

- 在 实体类中 添加 DateTimeFormat 注解

```java
@DateTimeFormat(pattern = "yyyy-MM-dd")
private Date createDate;
```

- 在形式参数中添加注解

```java
@RequestParam("createDate")@DateTimeFormat(pattern = "yyyy-MM-dd") Date createDate
```

