---
created: 2022-05-26 16:16
modified: 2024-04-24 10:07
tags:
  - Spring
aliases:
  - Controller 层方法参数解析器 - HandlerMethodArgumentResolver
---

`@JsonParam`

```java
/**
 * 作用在类上等价于该类所有方法参数都使用该注解
 * 作用在方法上等价于该方法所有参数都使用该注解
 **/
@Target({ElementType.TYPE, ElementType.METHOD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
public @interface JsonParam {

    boolean required() default false;

    String name() default "";

    String defaultValue() default "";

}
```

`JsonHandlerMethodArgumentResolver`

```java
@Component
public class JsonHandlerMethodArgumentResolver implements HandlerMethodArgumentResolver {


    private static final String REQUEST_KEY = "JSONPARAM";

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.hasParameterAnnotation(JsonParam.class) || parameter.hasMethodAnnotation(JsonParam.class)
                || Objects.requireNonNull(parameter.getMethod()).getDeclaringClass().getAnnotation(JsonParam.class) != null;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        JsonParam jsonParamAnnotation = parameter.getParameterAnnotation(JsonParam.class);
        if (jsonParamAnnotation == null) {
            jsonParamAnnotation = parameter.getMethodAnnotation(JsonParam.class);
        }
        if (jsonParamAnnotation == null) {
            jsonParamAnnotation = parameter.getDeclaringClass().getAnnotation(JsonParam.class);
        }
        HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
        JSONObject jsonObject;
        if (request == null) {
            return null;
        }
        if (request.getAttribute(REQUEST_KEY) != null) {
            jsonObject = (JSONObject) request.getAttribute(REQUEST_KEY);
        } else {
            try ( BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(request.getInputStream(), StandardCharsets.UTF_8))){
                StringBuilder jsonStr = new StringBuilder();
                String lineStr;
                while (bufferedReader.ready()) {
                    lineStr = bufferedReader.readLine();
                    jsonStr.append(lineStr);
                }
                jsonObject = JSON.parseObject(jsonStr.toString());
            } catch (ClassCastException | JSONException e) {
                throw new ParamJsonException(PARAM_ERROR.getResultMsg());
            }
            request.setAttribute(REQUEST_KEY, jsonObject);
        }

        if (jsonParamAnnotation.required()) {
            if (jsonObject == null) {
                throw new ParamJsonException(PARAM_ERROR.getResultMsg());
            }
            String key = StringUtils.isNotBlank(jsonParamAnnotation.name()) ? jsonParamAnnotation.name() : parameter.getParameterName();
            Method method = parameter.getMethod();
            if (!jsonObject.containsKey(key)) {
                if( method != null && method.getParameterCount() == 1) {
                return JSON.toJavaObject(jsonObject, parameter.getParameterType());
            } else {
                    throw new ParamJsonException(PARAM_ERROR.getResultMsg());
                }
            }
            return returnValue(parameter, jsonParamAnnotation, jsonObject, key);
        }
        if (jsonObject == null) {
            return getDefaultValue(parameter.getParameterType(), jsonParamAnnotation.defaultValue());
        }
        String key = StringUtils.isNotBlank(jsonParamAnnotation.name()) ? jsonParamAnnotation.name() : parameter.getParameterName();
        return returnValue(parameter, jsonParamAnnotation, jsonObject, key);
    }

    private Object returnValue(MethodParameter parameter, JsonParam jsonParamAnnotation, JSONObject jsonObject, String key) {
        Object object = jsonObject.getObject(key, parameter.getParameterType());
        object = object == null && parameter.getMethod().getParameterCount() == 1 ? JSON.toJavaObject(jsonObject, parameter.getParameterType()) : object;
        return object == null ? getDefaultValue(parameter.getParameterType(), jsonParamAnnotation.defaultValue()) : object;
    }


    static Object getDefaultValue(Class<?> clazz, String valueStr) {
        if (null == valueStr) {
            valueStr = "";
        }
        if (clazz == String.class) {
            return valueStr;
        } else if (clazz == Integer.class) {
            return "".equals(valueStr) ? Integer.valueOf(0) : Integer.valueOf(valueStr);
        } else if (clazz == Long.class) {
            return "".equals(valueStr) ? Long.valueOf(0) : Long.valueOf(valueStr);
        } else if (clazz == Short.class) {
            return "".equals(valueStr) ? Short.valueOf("0") : Short.valueOf(valueStr);
        } else if (clazz == Character.class) {
            return "".equals(valueStr) ? Character.valueOf(' ') : Character.valueOf(valueStr.charAt(0));
        } else if (clazz == Boolean.class) {
            return "".equals(valueStr) ? Boolean.FALSE : Boolean.valueOf(valueStr);
        } else if (clazz == Double.class) {
            return "".equals(valueStr) ? Double.valueOf("0") : Double.valueOf(valueStr);
        } else if (clazz == Float.class) {
            return "".equals(valueStr) ? Float.valueOf("0") : Float.valueOf(valueStr);
        } else if (clazz == BigDecimal.class) {
            return "".equals(valueStr) ? BigDecimal.ZERO : new BigDecimal(valueStr);
        } else if (clazz == BigInteger.class) {
            return "".equals(valueStr) ? BigInteger.ZERO : new BigInteger(valueStr);
        } else {
            return null;
        }
    }
}
```

> [!link] 参考链接
> [HandlerMethodArgumentResolver(参数解析器)的作用+使用小案例](https://blog.csdn.net/qq_36408229/article/details/104436508)
