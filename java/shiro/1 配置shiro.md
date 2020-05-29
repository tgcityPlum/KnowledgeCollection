# 配置shiro

## pom文件中配置
```
<!-- shiro -->
       <dependency>
           <groupId>org.apache.shiro</groupId>
           <artifactId>shiro-core</artifactId>
           <version>${shiro.version}</version>
       </dependency>
       <dependency>
           <groupId>org.apache.shiro</groupId>
           <artifactId>shiro-spring</artifactId>
           <version>${shiro.version}</version>
       </dependency>
```

## property.yml文件中配置
```
shiro:
  loginUrl: ${shiro.loginUrl}
  filterMapList: ${shiro.filterMapList}
```

## 注入shiro配置
```
@Configuration
public class MyShiroConfig {

    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("DefaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager) {
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);
        //添加Shiro内置过滤器
        return bean;
    }

    @Bean("DefaultWebSecurityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("MyShiroRealm") MyShiroRealm userRealm) {
        //@Qualifier 选择和参数中同名的bean
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //设置关联Realm
        securityManager.setRealm(userRealm);
        return securityManager;
    }

    @Bean("MyShiroRealm")
    public MyShiroRealm getRealm() {
        return new MyShiroRealm();
    }
}
```

## 基础AuthorizingRealm
```
@Component
@Slf4j
public class MyShiroRealm extends AuthorizingRealm {

    @Autowired
    private AccountMapper accountMapper;

    /**
     * 授权(验证权限时调用)
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        log.debug("------PC------shiro授权---------------");
        //在这里进行一些授权 分发权限等等
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //设置权限
        info.addStringPermission("user:root");
        return info;
    }

    /**
     * 认证(登录时调用)
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authcToken) throws AuthenticationException {
        log.debug("-------PC-----shiro认证---------------");
        UsernamePasswordToken token = (UsernamePasswordToken) authcToken;
        //查询用户信息
        AccountEntity account = accountMapper.selectOne(new QueryWrapper<AccountEntity>().eq("user_account", token.getUsername()));
        //账号不存在
        if (account == null) {
            throw new UnknownAccountException("账号或密码不正确");
        }
        return new SimpleAuthenticationInfo(account, account.getPassword(), ByteSource.Util.bytes(account.getPasswordEncryption()), getName());
    }

    /**
     * 凭证匹配器
     *
     * @param credentialsMatcher
     */
    @Override
    public void setCredentialsMatcher(CredentialsMatcher credentialsMatcher) {
        HashedCredentialsMatcher shaCredentialsMatcher = new HashedCredentialsMatcher();
        shaCredentialsMatcher.setHashAlgorithmName(ShiroUtils.hashAlgorithmName);
        shaCredentialsMatcher.setHashIterations(ShiroUtils.hashIterations);
        super.setCredentialsMatcher(shaCredentialsMatcher);
    }
}
```

## 登录时的使用
```
//登录（shiro）
       try {
           Subject subject = ShiroUtils.getSubject();
           UsernamePasswordToken token = new UsernamePasswordToken(account, password);
           subject.login(token);

//            Map<String, Object> map = new HashMap<>(1);
           //用户基本信息
           AccountEntity user = (AccountEntity) SecurityUtils.getSubject().getPrincipal();
           LoginUserResponse loginUserResponse = LoginUserResponse.of();
           BeanUtils.copyProperties(user, loginUserResponse);
//            map.put("account", loginUserResponse);

           return BaseResponse.ok(loginUserResponse);
       } catch (UnknownAccountException e) {
           return BaseResponse.code(403).msg(e.getMessage()).build();
       } catch (IncorrectCredentialsException e) {
           return BaseResponse.buildSuccess(Message.INVALID_USERNAME_OR_PASSWORD).build();
       } catch (LockedAccountException e) {
           return BaseResponse.code(400).msg("账号已被锁定,请联系管理员").build();
       } catch (AuthenticationException e) {
           return BaseResponse.buildSuccess(Message.ACCOUNT_VERIFICATION_FAILED).build();
       } catch (Exception e) {
           return BaseResponse.code(400).msg("网络异常,请联系管理员").build();
       }
```

## 在线的校验
```
AccountEntity user = (AccountEntity) SecurityUtils.getSubject().getPrincipal();
        if (user == null) {
            return BaseResponse.buildSuccess(Message.NOT_LOGGED_IN).build();
        }
```
