# JSR Validator

## 自定义Validator
```java
return Validation.byDefaultProvider().configure()
                .messageInterpolator(
                        new ResourceBundleMessageInterpolator(new PlatformResourceBundleLocator("ValidationMessages")))
                .buildValidatorFactory().getValidator();
```

## validate
javax.validation.Validator#validate

通过实现HandlerMethodArgumentResolver，重新解析参数，自定义校验。

参考：

org.springframework.web.servlet.mvc.method.annotation.AbstractMessageConverterMethodArgumentResolver#validateIfApplicable
org.springframework.validation.DataBinder#validate(java.lang.Object...)
org.springframework.validation.beanvalidation.SpringValidatorAdapter#validate(java.lang.Object, org.springframework.validation.Errors, java.lang.Object...)
org.springframework.validation.beanvalidation.SpringValidatorAdapter#asValidationGroups
```java
@Slf4j
public class ParamMethodArgumentResolver implements HandlerMethodArgumentResolver {

    /**
     * 执行条件，同时使用Validated和RequestBody
     */
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.hasParameterAnnotation(Validated.class) && parameter.hasParameterAnnotation(RequestBody.class);
    }

    @SuppressWarnings("ConstantConditions")
    @SneakyThrows
    @Override
    public Object resolveArgument(@Nullable MethodParameter parameter, ModelAndViewContainer mavContainer, @Nullable NativeWebRequest webRequest, WebDataBinderFactory binderFactory) {
        HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
        Locale locale = LanguageUtil.getLocale(request);
        Class<?> type = parameter.getParameterType();

        // 解析
        Annotation[] annotations = parameter.getParameterAnnotations();
        for (Annotation ann : annotations) {
            Validated validatedAnn = AnnotationUtils.getAnnotation(ann, Validated.class);
            if (validatedAnn != null || ann.annotationType().getSimpleName().startsWith("Valid")) {
                Object hints = (validatedAnn != null ? validatedAnn.value() : AnnotationUtils.getValue(ann));
                Object[] validationHints = (hints instanceof Object[] ? (Object[]) hints : new Object[] {hints});
                Class<?>[] groups = asValidationGroups(validationHints);
                Object obj = JsonUtils.jsonToObject((String) getRequestBody(request), (Type) type);
                // 验证必填
                jsr303(obj, groups, locale);
                return obj;
            }
        }
        return JsonUtils.jsonToObject((String) getRequestBody(request), (Type) type);
    }

    /**
     * 获取分组
     */
    private Class<?>[] asValidationGroups(Object... validationHints) {
        Set<Class<?>> groups = new LinkedHashSet<>(4);
        for (Object hint : validationHints) {
            if (hint instanceof Class) {
                groups.add((Class<?>) hint);
            }
        }
        return ClassUtils.toClassArray(groups);
    }

    /**
     * 参数校验
     * @param obj 对象
     * @param groups 分组
     * @param locale 时区
     */
    private void jsr303(Object obj, Class<?>[] groups, Locale locale) {
        Set violations = Jsr303Util.getValidatorByLocale(locale).validate(obj, groups);
       // 自定义
    }

    @Nullable
    @SneakyThrows
    private Object getRequestBody(HttpServletRequest request) {
        BufferedReader reader = request.getReader();
        StringBuilder builder = new StringBuilder();
        String line = reader.readLine();
        while(line != null){
            builder.append(line);
            line = reader.readLine();
        }
        reader.close();
        return builder.toString();
    }

}

```
