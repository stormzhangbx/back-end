# 使用Validate

[【SpringBoot】 在SpringBoot中使用Hibernate Validate](https://blog.csdn.net/xuhuanfeng232/article/details/83894822)
[使用spring validation完成数据后端校验](https://blog.csdn.net/u013815546/article/details/77248003)

设置当校验出现第一个不满足情况时，后面的属性就不再校验（默认校验策略是依次校验，并且将不通过的结果保存，最后再统一抛出异常信息）

```java
@Configuration
public class ValidatorConfig {

    @Bean
    public Validator validator() {
        ValidatorFactory factory = Validation.byProvider(HibernateValidator.class)
                .configure()
                // 将fail_fast设置为true即可，如果想验证全部，则设置为false或者取消配置即可
                .addProperty("hibernate.validator.fail_fast", "true")
                .buildValidatorFactory();
        return factory.getValidator();
    }
}
```

## 校验@RequestBody标注的参数

```java
public class User {

    @NotBlank(message = "用户名不能为空")
    private String name;

    @Max(value = 120, message = "年龄不能超过120岁")
    private int age;

    @NotNull
    @Size(min = 8, max = 20, message = "密码必须大于8位并且小于20位")
    private String password;

    @Email(message = "请输入符合格式的邮箱")
    private String email;

    // set、get方法
}
```

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public User addUser(@Valid @RequestBody User user) {
        // 仅测试验证过程，省略其他的逻辑
        return user;
    }
}
```

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResultInfo<?> validationErrorHandler(MethodArgumentNotValidException ex) {
        // 同样是获取BindingResult对象，然后获取其中的错误信息
        // 如果前面开启了fail_fast，事实上这里只会有一个信息
        //如果没有，则可能又多个
        List<String> errorInformation = ex.getBindingResult().getAllErrors()
                .stream()
                .map(ObjectError::getDefaultMessage)
                .collect(Collectors.toList());
        return new ResultInfo<>(400, errorInformation.toString(), null);
    }
}
```

这里的ResultInfo是自定义的一个结果对象，用于作为统一的返回对象，内容如下

```java
public class ResultInfo<T> {
    private int code;
    private String message;
    private T body;

    public ResultInfo(int code, String message, T body) {
        this.code = code;
        this.message = message;
        this.body = body;
    }

    public ResultInfo(int code, String message) {
        this(code, message, null);
    }

    public ResultInfo(String message) {
        this(200, message, null);
    }
}
// get、set方法
```

## 校验 `@PathVariable`、`@RequestParam`标注的参数

为了让校验生效，需要在控制器类上使用`@Validated`

```java
@RestController
@RequestMapping("/users")
@Validated
public class UserController {

    // ...

    @GetMapping("/{name}")
    public User getUserByName(
                    @NotNull
                    @Size(min = 1, max = 20, message = "用户名格式有误")
                    @PathVariable String name) {
        User user = new User();
        user.setName(name);
        return user;
    }

    @GetMapping
    public User getUserByNameParam(
                    @NotNull
                    @Size(min = 1, max = 20, message = "用户名格式有误")
                    @RequestParam("name") String name) {
        User user = new User();
        user.setName(name);
        return user;
    }
}
```

注意，此时如果验证失败，会抛出异常信息，但是异常类型不是`MethodArgumentNotValidException`，而是`ConstraintViolationException`，所以

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    // ...

    @ExceptionHandler(ConstraintViolationException.class)
    public ResultInfo<?> validationErrorHandler(ConstraintViolationException ex) {
        List<String> errorInformation = ex.getConstraintViolations()
                .stream()
                .map(ConstraintViolation::getMessage)
                .collect(Collectors.toList());
        return new ResultInfo<>(400, errorInformation.toString(), null);
    }

}
```
