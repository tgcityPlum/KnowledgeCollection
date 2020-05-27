# 配置swagger

## pom配置
```
<!-- swagger -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>${swagger.version}</version>
            <exclusions>
                <exclusion>
                    <groupId>io.swagger</groupId>
                    <artifactId>swagger-annotations</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>io.swagger</groupId>
                    <artifactId>swagger-models</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>${swagger.version}</version>
        </dependency>
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-annotations</artifactId>
            <version>${swagger.annotations}</version>
        </dependency>
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-models</artifactId>
            <version>${swagger.models}</version>
        </dependency>
```

## 配置启动文件
```
/**
 * @author: TGCity
 * @create: 2020/5/27
 * @description Swagger的配置
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {

  /**
  * 定义分隔符,配置Swagger多包
  */
 private static final String CONTROLLER_PATH = "com.tgcity.example.demo1.controller";

 @Bean
 public Docket createMobileRestApi() {

     return new Docket(DocumentationType.SWAGGER_2)
             .groupName("移动端接口")
             .apiInfo(apiInfo("Water Mobile Rest Api", "水务移动端接口","1.0"))
             .useDefaultResponseMessages(true)
             .forCodeGeneration(false)
             .select()
             .apis(RequestHandlerSelectors.basePackage(CONTROLLER_PATH))
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
             .paths(PathSelectors.regex("/mobile.*"))
             .build();
 }

 @Bean
 public Docket createFrontEndRestApi() {

     return new Docket(DocumentationType.SWAGGER_2)
             .groupName("前端接口")
             .apiInfo(apiInfo("Water Front End Rest Api", "水务前端接口","1.0"))
             .select()
             .apis(RequestHandlerSelectors.basePackage(CONTROLLER_PATH))
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
             .paths(PathSelectors.regex("/frontend.*"))
             .build();
 }

 @Bean
 public Docket createBackEndRestApi() {

     return new Docket(DocumentationType.SWAGGER_2)
             .groupName("后端接口")
             .apiInfo(apiInfo("Water Front End Rest Api", "水务后端接口","1.0"))
             .select()
             .apis(RequestHandlerSelectors.basePackage(CONTROLLER_PATH))
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
             .paths(PathSelectors.regex("/backend.*"))
             .build();
 }

 private ApiInfo apiInfo(String title, String description, String version) {
     return new ApiInfoBuilder()
             .title(title)
             .description(description)
             .termsOfServiceUrl("")
             .license("Apache 2.0")
             .licenseUrl("http://www.apache.org/licenses/LICENSE-2.0.html")
             .version(version)
             .build();
 }

}
```

## control层使用注解
```

@RestController
@RequestMapping("/mobile/test/stable")
@Api(tags = "1、常见请求的测试")
@Slf4j
public class StableController {

    /**
     * 直接get请求测试
     */
    @GetMapping("get/parameter/no")
    @ApiOperation(value = "get无参测试", httpMethod = "GET", consumes = MediaType.APPLICATION_JSON_VALUE)
    public String getData() {
        Map<String, Object> map = new HashMap<>(2);
        map.put("code", 1);
        map.put("message", "get返回Data成功");
        return JSONObject.toJSONString(map);
    }

    /**
     * 携参方式一get请求测试
     *
     * @param phone string
     */
    @GetMapping("get/parameter/static")
    @ApiOperation(value = "get携参测试", httpMethod = "GET", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
    @ApiImplicitParam(name = "phone", value = "手机号", dataType = "string", required = true)
    public String getDataParamStyleOne(@RequestParam("phone") String phone) {
        log.info("phone=" + phone);
        Map<String, Object> map = new HashMap<>(2);
        map.put("code", 1);
        map.put("message", "get返回Data成功");
        return JSONObject.toJSONString(map);
    }

    /**
     * 携参方式二get请求测试
     *
     * @param phone string
     */
    @GetMapping("get/parameter/dynamic/{phone}")
    @ApiOperation(value = "get动态携参测试", httpMethod = "GET", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public String getDataParamStyleTwo(@PathVariable("phone") String phone) {
        log.info("phone=" + phone);
        Map<String, Object> map = new HashMap<>(2);
        map.put("code", 1);
        map.put("message", "get返回Data成功");
        return JSONObject.toJSONString(map);
    }

    /**
     * 携参post测试
     *
     * @param pageNo   int
     * @param pageSize int
     */
    @PostMapping(value = "post/parameter", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    @ApiOperation(value = "post多参测试", httpMethod = "POST", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    @ApiImplicitParams(value = {
            @ApiImplicitParam(name = "pageNo", value = "页号", dataType = "int", required = true),
            @ApiImplicitParam(name = "pageSize", value = "数量", dataType = "int", required = true)
    })
    public String postData(int pageNo, int pageSize) {
        log.info("pageNo=" + pageNo + ",pageSize=" + pageSize);
        Map<String, Object> map = new HashMap<>(2);
        map.put("code", 1);
        map.put("message", "post返回Data成功");
        return JSONObject.toJSONString(map);
    }

    /**
     * 携对象post测试
     *
     * @param testPostReq TestPostReq
     */
    @PostMapping(value = "post/body")
    @ApiOperation(value = "post对象测试", httpMethod = "POST", consumes = MediaType.APPLICATION_JSON_VALUE)
    @ApiImplicitParam(name = "testPostReq",value = "请求体",dataType = "TestPostReq",required = true)
    public String postDataBody(@RequestBody TestPostReq testPostReq) {
        log.info("testPostReq=" + testPostReq.toString());
        Map<String, Object> map = new HashMap<>(2);
        map.put("code", 1);
        map.put("message", "post返回Data成功");
        return JSONObject.toJSONString(map);
    }
}
```
