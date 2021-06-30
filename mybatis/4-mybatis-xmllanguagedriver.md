# 4 Mybatis-XMLLanguageDriver扩展

## @Select注解中使用IN查询

Mybatis原生的@Select注解并不支持IN查询，但是提供了Mybatis中提供了LanguageDriver，我们可用手动设置SqlSource。在这里，LanguageDriver的默认实现类为XMLLanguageDriver和RawLanguageDriver。另外还可以对@Insert和@Update增强，但是如果你使用tkMybatis或MybatisPlus，这些没必要了，其原理是一样的，具体的原理可阅读[Mybatis增强型注解简化SQL语句\(一\)](https://blog.csdn.net/ExcellentYuXiao/article/details/53262928)

### 继承XMLLanguageDriver

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import org.apache.ibatis.mapping.SqlSource;
import org.apache.ibatis.scripting.LanguageDriver;
import org.apache.ibatis.scripting.xmltags.XMLLanguageDriver;
import org.apache.ibatis.session.Configuration;

public class SimpleSelectInExtendedLanguageDriver extends XMLLanguageDriver implements LanguageDriver {

    private static final Pattern inPattern = Pattern.compile("\\(#\\{(\\w+)\\}\\)");

    @Override
    public SqlSource createSqlSource(Configuration configuration, String script, Class<?> parameterType) {

        Matcher matcher = inPattern.matcher(script);
        if (matcher.find()) {
            script = matcher.replaceAll("<foreach collection=\"$1\" item=\"_item\" open=\"(\" "
                    + "separator=\",\" close=\")\" >#{_item}</foreach>");
        }

        script = "<script>" + script + "</script>";
        return super.createSqlSource(configuration, script, parameterType);
    }
}
```

### 使用

```java
@Select("SELECT * FROM T WHERE id IN (#{ids})")
@Lang(SimpleSelectInExtendedLanguageDriver.class) // 使用
List<User> listBy(@Param("ids") List<Integer> ids);
```

