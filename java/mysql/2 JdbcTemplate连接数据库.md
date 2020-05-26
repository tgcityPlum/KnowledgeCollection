# JdbcTemplate连接数据库

## 导入依赖
在pom.xml的引入依赖
```
<!--   mysql     -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
```

## 配置数据库

在application.properties中引入
```
#数据库
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://192.168.15.36:3306/water?serverTimezone=GMT%2B8&useSSL=false&allowMultiQueries=true&useUnicode=true&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.max-idle=10
spring.datasource.max-wait=10000
spring.datasource.min-idle=5
spring.datasource.initial-size=5
```

## Controller层代码
在Controller层中设置代码
```
/**
 * @author: TGCity
 * @create: 2020/5/26
 * @description 测试数据库连接
 */
@RestController
@RequestMapping("/test/database")
public class TestDatabaseController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    /**
     * 测试数据库连接情况
     */
    @GetMapping("/getData")
    public List<Map<String, Object>> getData() {
        String sql = "SELECT * FROM `user`;";
        return jdbcTemplate.queryForList(sql);
    }
}
```
