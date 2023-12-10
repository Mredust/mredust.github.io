# Swagger使用教程



## 	1.依赖引入

​	swagger、swagger-ui | swagger-bootstrap-ui

```xml
<!-- swagger -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
  <groupId>com.github.xiaoymin</groupId>
  <artifactId>swagger-bootstrap-ui</artifactId>
  <version>1.9.6</version>
</dependency>
```

​	

## 	2.新建配置类：SwaggerConfig

```java
import com.github.xiaoymin.swaggerbootstrapui.annotations.EnableSwaggerBootstrapUI;
import com.google.common.base.Predicates;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;


@Configuration
@EnableSwagger2
@EnableSwaggerBootstrapUI
public class SwaggerConfig {
    @Bean
    public Docket api() {
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .pathMapping("/") // 设置api地址的前缀
        .select() // 选择哪些路径和api会生成document
        .apis(RequestHandlerSelectors.any()) // 对所有api进行监控
        .paths(Predicates.not(PathSelectors.regex("/error.*"))) // 排除错误路径不监控
        .paths(PathSelectors.regex(("/.*"))) // 对根下所有路径进行监控
        .build();
    }
    
    // 基本信息设置
    private ApiInfo apiInfo() {
    return new ApiInfoBuilder()
        .title("学生成绩管理后台-接口文档") // 标题
        .description("学生、学科、成绩的增删改查") // 描述
        .termsOfServiceUrl("http://localhost:8080/") // 服务条款
        .license("Swagger-的使用详细教程")
        .licenseUrl("null")
        .contact(new Contact("Mredust", null, "3130383647@qq.com")) // 联系方式
        .version("1.0") // 版本
        .build();
    }
}
```



## 3.Interception拦截排除

```java
.excludePathPatterns(
    "/swagger-resources/**",
    "/webjars/**",
    "/v2/**",
    "/swagger-ui.html/**",
    "/doc.html/**"
);
```



> 地址访问
>
> swagger-ui: http://localhost:4444/swagger-ui.html#/
>
> swagger-bootstrap-ui:  http://localhost:4444/doc.html







## 4.注解说明

|        名字        |                  描述                  |
| :----------------: | :------------------------------------: |
|        [@Api](#api)        |                 类标识                 |
|     [@ApiIgnore](#apiignore)     |           忽略该Controller类           |
|   [@ApiOperation](#apioperation)    |      Controller类中的 method接口       |
|     [@ApiParam ](#apiparam)     | 单个参数，写在@RequestParam...参数左侧 |
| [@ApiImplicitParam](#apiimplicitparam)  |              单个请求参数              |
| [@ApiImplicitParams](#apiimplicitparams) |              多个请求参数              |
|      [@ApiMoel](#apimodel)      |                 实体类                 |
| [@ApiModelProperty](#apimodelproperty)  |               实体类字段               |
|    [@ApiResponse](#apiresponse)    |              单个参数响应              |
|   [@ApiResponses](#apiresponses)    |                多个响应                |
|   [@Authorization](#authorization)   |         声明的资源或操作上授权         |
|     [@ApiError](#apierror)      |          接口错误所返回的信息          |





### <span id="apiignore">@apiignore</span>

> 可以不被swagger显示在页面上



===========================================



### <span id="api">@Api</span>

> tags：表示说明
> value：也是说明，可以使用tags替代

### <span id="apioperation">@apioperation</span>

> value：用于方法描述
> notes：用于提示内容
> tags：可以重新分组

### <span id="apiparam">@apiparam</span>

> name：参数名
> value：参数说明
> required：是否必填

```java
@Api(value="用户controller",tags={"用户操作接口"})
@RestController
public class UserController {
    @ApiOperation("更改用户信息")
    @PostMapping("/updateUserInfo")
    public int UserInfo(@RequestBody @ApiParam(name="用户对象",value="传入json格式",required=true) User user){     }
}
```

### <span id="apiimplicitparam">@apiimplicitparam</span>

> 表示单独的请求参数

### <span id="apiimplicitparams">@apiimplicitparams</span>
> 包含多个 @ApiImplicitParam
> name：参数
> value：参数说明
> dataType：数据类型
> paramType：参数类型
> example：举例说明

```java
@ApiOperation("查询测试")
@GetMapping("select")
//@ApiImplicitParam(name="name",value="用户名",dataType="String", paramType = "query")
@ApiImplicitParams({
@ApiImplicitParam(name="name",value="用户名",dataType="string", paramType ="query",example="Mredust"),
@ApiImplicitParam(name="id",value="用户id",dataType="long", paramType = "query")
})
public void select(){}
```



===========================================



### <span id="apimodel">@apimodel</span>

> value：表示对象名
> description：描述
> 都可省略

### <span id="apimodelproperty">@apimodelproperty</span>

> value：字段说明
> name：重写属性名字
> dataType：重写属性类型
> required：是否必填
> example：举例说明
> hidden：隐藏

```java
@ApiModel(value="user对象",description="用户对象user")
public class User implements Serializable{
    @ApiModelProperty(value="用户名",name="username",example="Mredust")
    private String username;

    @ApiModelProperty(value="状态",name="state",required=true)
    private Integer state;

    @ApiModelProperty(value="id数组",hidden=true)
    private String[] ids;
}
```



### <span id="apiresponse">@apiresponse</span>

### <span id="apiresponses">@apiresponses</span>
### <span id="authorization">@authorization</span>
### <span id="apierror">@apierror</span>

