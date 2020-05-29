# validator配置

## pom文件配置
```
<!-- validator -->
       <dependency>
           <groupId>javax.validation</groupId>
           <artifactId>validation-api</artifactId>
           <version>2.0.1.Final</version>
       </dependency>

       <dependency>
           <groupId>org.hibernate</groupId>
           <artifactId>hibernate-validator</artifactId>
           <version>6.0.13.Final</version>
       </dependency>
```

## 注入bean
```
@Configuration
public class FactoryConfig {

    @Bean
    public MethodValidationPostProcessor methodValidationPostProcessor() {
        return new MethodValidationPostProcessor();
    }
}
```

## Controller添加校验注解
```
@PutMapping("resetpassword")
    @ApiOperation(value = "修改用户密码", httpMethod = "PUT", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
    @ApiImplicitParam(name = "request", value = "请求体", required = true, dataType = "ResetPasswordReq")
    public BaseResponse resetPassword(@Valid @RequestBody ResetPasswordReq request) {
        return BaseResponse.ok().build();
    }
```

## bean中添加校验规则
```
@Data
@ApiModel(description = "请求体")
public class ResetPasswordReq {

    @ApiModelProperty(name = "oldPassword", value = "原密码", required = true)
    @NotBlank(message = "原密码不能为空")
    private String oldPassword;

    @ApiModelProperty(name = "newPassword", value = "新密码", required = true)
    @NotBlank(message = "新密码不能为空")
    private String newPassword;

    @ApiModelProperty(name = "confirmPassword", value = "确认密码", required = true)
    @NotBlank(message = "确认密码不能为空")
    private String confirmPassword;
}
```
