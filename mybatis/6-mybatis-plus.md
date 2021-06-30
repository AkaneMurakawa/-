# Mybatis-Plus

## 文档

[https://baomidou.com/guide/](https://baomidou.com/guide/)

## 配置

### 日志

```text
# 方式一
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl 

# 方式二 application.yml 中增加配置，指定 mapper 文件所在的包
logging:
  level:
    com.baomidou.example.mapper: debug
```

### id

```text
    @TableId(value = "id", type = IdType.AUTO)
    public Long id;
```

### @TableField

[https://baomidou.com/guide/auto-fill-metainfo.html](https://baomidou.com/guide/auto-fill-metainfo.html)

```text
@TableField(fill=FieldFill.INSERT)

@TableField(value = "create_time", fill = FieldFill.INSERT)
private Long createTime;

@TableField(value = "update_time", fill = FieldFill.INSERT_UPDATE)
private Long updateTime;

@Version
@TableField(value = "version", fill = FieldFill.INSERT_UPDATE, update = "%s+1")
private Integer version;
```

实现MetaObjectHandler

```text
示例：this.strictInsertFill(metaObject, "createTime", () -> LocalDateTime.now(), LocalDateTime.class); // 起始版本 3.3.3(推荐)

@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    public static final String CREATE_USER = "createUser";
    public static final String CREATE_TIME = "createTime";
    public static final String UPDATE_USER = "updateUser";
    public static final String UPDATE_TIME = "updateTime";
    public static final String VERSION = "version";

    @Override
    public void insertFill(MetaObject metaObject) {
        modifyFill(metaObject, CREATE_USER, CREATE_TIME);
        fill(metaObject, VERSION, Integer.class, 1);
        modifyFill(metaObject, UPDATE_USER, UPDATE_TIME);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        modifyFill(metaObject, UPDATE_USER, UPDATE_TIME);
    }

    private void modifyFill(MetaObject metaObject, String userField, String timeField) {
        long timeStamp = System.currentTimeMillis();
        fill(metaObject, timeField, Long.class, timeStamp);
    }

    public <T> void fill(MetaObject metaObject, String fieldName, Class<T> clazz, T value) {
        boolean hasSetter = metaObject.hasSetter(fieldName);
        boolean originNoneVal = metaObject.getValue(fieldName) == null;
        if (hasSetter && originNoneVal && null != value && StringUtils.isNoneBlank(value.toString())) {
            this.strictInsertFill(metaObject, fieldName, clazz, value);
        }
    }
}
```

### 乐观锁

```text
@Version
```

```java
// Spring Boot 方式
@Configuration
@MapperScan("按需修改")
public class MybatisPlusConfig {    
    /**
     * 新版
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

### 分页

[https://baomidou.com/guide/page.html](https://baomidou.com/guide/page.html)

```text
 // 最新版
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));
        return interceptor;
    }
```

### 逻辑删除

```text
@TableLogic
private Integer deleted;
```

