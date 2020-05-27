# Mybatis连接数据库

## pom配置
```
<!-- mybatis-plus -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>${mybatis-plus.version}</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>${mybatis-plus.version}</version>
        </dependency>
```

## application.yml配置
```
# 设置mybatis
mybatis-plus:
  mapper-locations: classpath:mappers/**/*.xml
  typeAliasesPackage: com.tgcity.example.demo1.dal.entity
  global-config:
    db-config:
      logic-delete-value: true # 逻辑已删除值(默认为 true)
      logic-not-delete-value: false # 逻辑未删除值(默认为 false)
      updateStrategy: IGNORED  #更新的时候，设置为忽略策略，可以更新字段为null的值
  configuration:
    map-underscore-to-camel-case: true
    call-setters-on-nulls: true
```

## resources中配置mappers
以user.xml举例
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.tgcity.example.demo1.dal.mappers.user.UserMapper">

    <select id="getUserName" resultType="java.lang.String">
        SELECT
            name
        FROM
            user
        where
            id = #{id}
    </select>

    <select id="getUserList" resultType="com.tgcity.example.demo1.dal.entity.user.UserEntity">
        select
            *
        from
            user
    </select>

</mapper>

```

## dal层mapper配置
以UserMapper举例
```
/**
 * @author: TGCity
 * @create: 2020/5/26
 * @description 用户查询数据库的接口
 */
@Repository
public interface UserMapper extends BaseMapper<UserEntity> {

    /**
     * 获取制定用户名称
     *
     * @return String
     */
    String getUserName(@Param("id") int id);

    /**
     * 获取所有用户
     */
    List<UserEntity> getUserList();
}
```

## service层配置service
以UserService举例
```
/**
 * @author: TGCity
 * @create: 2020/5/26
 * @description 用户向外部提供的接口
 */
public interface UserService {

    /**
     * 获取用户名称
     *
     * @return String
     * @throws Exception Exception
     */
    String getUserName() throws Exception;

    /**
     * 获取所有用户
     *
     * @return List<UserEntity>
     * @throws Exception Exception
     */
    List<UserEntity> getUserList() throws Exception;

}
```

## service层配置impl类
以UserServiceImpl举例
```
/**
 * @author: TGCity
 * @create: 2020/5/26
 * @description 用户操作向外部的类。可以处理校验
 */
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public String getUserName() throws Exception {
        return userMapper.getUserName(1);
    }

    @Override
    public List<UserEntity> getUserList() throws Exception {
        return userMapper.getUserList();
    }

}
```

## Controller层配置Controller
以TestDatabaseController举例
```
/**
 * @author: TGCity
 * @create: 2020/5/26
 * @description 测试数据库连接
 */
@RestController
@RequestMapping("/test/database")
@Api(tags = "2、测试数据库连接")
public class TestDatabaseController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Autowired
    private UserService userService;

    /**
     * 测试数据库连接情况
     */
    @GetMapping("/getData")
    @ApiOperation(value = "jdbcTemplate连接测试", httpMethod = "GET", consumes = MediaType.APPLICATION_JSON_VALUE)
    public List<Map<String, Object>> getData() {
        String sql = "SELECT * FROM `user`;";
        return jdbcTemplate.queryForList(sql);
    }

    /**
     * 测试Mybatis连接数据库
     * 获取用户账号
     */
    @GetMapping("/getMybatisData")
    @ApiOperation(value = "获取用户名", httpMethod = "GET", consumes = MediaType.APPLICATION_JSON_VALUE)
    public String getMybatisData() {
        try {
            return userService.getUserName();
        } catch (Exception e) {
            e.printStackTrace();
        }

        return "";
    }

    /**
     * 获取用户列表
     */
    @GetMapping("/getAllUsers")
    @ApiOperation(value = "获取用户列表", httpMethod = "GET", consumes = MediaType.APPLICATION_JSON_VALUE)
    public List<UserEntity> getAllUsers() {
        try {
            return userService.getUserList();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return new ArrayList<>();
    }
}
```
