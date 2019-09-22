# 环境搭建 

* **添加Maven依赖**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--父依赖-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.4.RELEASE</version>
    </parent>

    <groupId>SpringBoot</groupId>
    <artifactId>SpringBoot</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.1.4.RELEASE</version>
        </dependency>
    </dependencies>

</project>
```

## SpringBoot启动类

* **创建`Application`类，类上使用注解`@SpringBootApplication`**
* **在`main`方法调用`SpringApplication.run(Application.class);`**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class);
    }
}
```

# 热部署

* **无需重写运行Tomcat即可运行修改后的代码**

## 引入依赖库

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <version>2.1.4.RELEASE</version>
</dependency>

```

## 开启自动编译

**步骤1**

![](https://leanote.com/api/file/getImage?fileId=5ccbf205ab64410aae002398)

**步骤2**

![](https://leanote.com/api/file/getImage?fileId=5ccbf3a3ab64410aae0023e7)



---

# 整合mybatis

## mybatis依赖

* **spring-boot-starter在后面不是spring-boot-starter启动器，而是mybatis提供的**

```xml
        <!--mybatis起步依赖-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.1</version>
        </dependency>
```

## SQL驱动

```xml
        <!-- MySQL连接驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.13</version>
        </dependency>
```

## SQL连接信息

```properties
#DB Configuration:
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?
useUnicode=true&characterEncoding=utf8
spring.datasource.username=root
spring.datasource.password=root
```

## mybatis连接信息

```properties
#pojo别名扫描包
mybatis.type-aliases-package=com.itheima.domain
#加载Mybatis映射文件
mybatis.mapper-locations=classpath:mapper/*.xml
```

## MapperScan注解

```java
@SpringBootApplication
@MapperScan("com.aib.dao")  //扫描Mapper接口
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



# 部署项目

* **添加打包插件**

```xml
    <!--打包成JAR包进行部署插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

* **打包**

![00](assets/微信截图_20190506232358.png)

* **执行JAR包**

```
java -jar xxx
```

# 配置SpringBoot

- **YML文件格式是YAML (YAML Aint Markup Language)编写的文件格式,YML文件是以数据为核心的，比传统的xml方式更加简洁**
- **YML文件的扩展名可以使用.yml或者.yaml**

## 配置数据

### 配置基本数据

- **key: value**
- **注意value前需要空格**

```
name: haohao
```

### 配置对象数据

```
person:
name: haohao
age: 31
addr: beijing
#或者
person: {name: haohao,age: 31,addr: beijing}

语法：
key:
key1: value1
key2: value2
或者：
key: {key1: value1,key2: value2}

注意：key1前面的空格个数不限定，在yml语法中，相同缩进代表同一个级别
```

### 配置数组&集合

```
语法：
key:
- value1
- value2
或者：
key: [value1,value2]


city:
- beijing
- tianjin
- shanghai
- chongqing
#或者
city: [beijing,tianjin,shanghai,chongqing]
#集合中的元素是对象形式
student:
- name: zhangsan
age: 18
score: 100
- name: lisi
age: 28
score: 88
- name: wangwu
age: 38
score: 90

```

## 获取配置数据

### @Value

* **注入实体类**

```java
    /**
     * <bean class="Person">
     *      <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#{SpEL}"></property>
     * <bean/>
     */

@Component
public class Person {
    @Value("${person.name}")
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



### @ConfigurationProperties

* **添加依赖，在编译项目时调用该处理器**

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```



* **在实体类上添加配置**

```java
@Component
//prefix添加前缀标识
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

# H5跨域

```java
@Configuration
public class CorsConfig {
    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*"); // 允许任何域名使用
        corsConfiguration.addAllowedHeader("*"); // 允许任何头
        corsConfiguration.addAllowedMethod("*"); // 允许任何方法（post、get等）
        return corsConfiguration;
    }

    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", buildConfig()); // 对接口配置跨域设置
        return new CorsFilter(source);
    }
}
```



# Token验证机制

* **引入依赖**

```XML
        <!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.8.0</version>
        </dependency>
```

* **控制器类**

```java
@Controller
public class TestController {
    @RequestMapping("/login")
    @ResponseBody
    public String login(String uid, String uname) {
        if (uid.equals("1") && uname.equals("admin")) {
            //生成Token
            String token = TokenUtil.signToken(uname, uid);
            return token;
        }
        return "登录失败";
    }

    @RequestMapping("/getUser")
    @ResponseBody
    public String getUser() {
        return "获取用户信息";
    }
}
```

* **Token工具类**

```java
public class TokenUtil {

    private static String tokenSecret = "123456";
    private static long time = 10 * 1000;

    /**
     * 生成Token
     *
     * @param uname
     * @param uid
     * @return
     */
    public static String signToken(String uname, String uid) {
        //过期时间
        Date date = new Date(System.currentTimeMillis() + time);
        //私钥及加密
        Algorithm algorithm = Algorithm.HMAC256(tokenSecret);
        //保存头部信息
        Map<String, Object> header = new HashMap<>();
        header.put("type", "JWT");
        header.put("algorithm", "HMAC256");
        //附带uname，uid参数生成签名
        return JWT.create()
                .withHeader(header)
                .withClaim("uname", uname)
                .withClaim("uid", uid)
                .withExpiresAt(date)
                .sign(algorithm);
    }

    /**
     * 验证Token是否有效
     *
     * @param token
     * @return
     */
    public static boolean verifyToken(String token) {
        try {
            Algorithm algorithm = Algorithm.HMAC256(tokenSecret);
            JWTVerifier jwtVerifier = JWT.require(algorithm).build();
            DecodedJWT decodedJWT = jwtVerifier.verify(token);
            return true;
        } catch (Exception e) {
            return false;
        }
    }
}
```

* **拦截器进行验证**

```java
@Component
public class TokenIntercepter implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("Token");
        if (token != null) {
            System.out.println("得到Token");
            boolean verifyToken = TokenUtil.verifyToken(token);
            if (verifyToken) {
                return true;
            }
        }
        response.setCharacterEncoding("utf-8");
        response.getWriter().write("Token Error");
        return false;
    }
}
```

* **添加拦截器配置**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Autowired
    TokenIntercepter intercepter;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(intercepter).addPathPatterns("/*").excludePathPatterns("/login","/error");
    }
}
```

# 图片服务器

```properties
#服务器访问图片路径
spring.mvc.static-path-pattern=/file/**
#图片存储真实路径
spring.resources.static-locations=file:D:/WebFileServer/test
```

