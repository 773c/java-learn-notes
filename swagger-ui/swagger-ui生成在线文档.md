#### 说明：Swagger-UI是HTML, Javascript, CSS的一个集合，可以动态地根据注解生成在线API文档。

##### 一、常用注解

* @Api：用于修饰Controller类，生成Controller相关文档信息
* @ApiOperation：用于修饰Controller类中的方法，生成接口方法相关文档信息
* @ApiParam：用于修饰接口中的参数，生成接口参数相关文档信息
* @ApiModelProperty：用于修饰实体类的属性，当实体类是请求参数或返回结果时，直接生成相关文档信息

例子：

```
@Api(tags = "PmsBrandController", description = "商品品牌管理")、
@ApiOperation("获取所有品牌列表")
```

##### 二、spring整合swagger-ui

```
<!--Swagger-UI API文档生产工具-->
<dependency>
   <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger2</artifactId>
  <version>2.7.0</version>
</dependency>
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger-ui</artifactId>
  <version>2.7.0</version>
</dependency>
```

##### 三、添加Swagger-UI的配置
注意：Swagger对生成API文档的范围有三种不同的选择

> （1）生成指定包下面的类的API文档
> （2）生成有指定注解的类的API文档
> （3）生成有指定注解的方法的API文档

代码：

```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**

 * Swagger2API文档的配置
   */
   @Configuration
   @EnableSwagger2
   public class Swagger2Config {
   @Bean
   public Docket createRestApi(){
       return new Docket(DocumentationType.SWAGGER_2)
               .apiInfo(apiInfo())
               .select()
               //为当前包下controller生成API文档
               .apis(RequestHandlerSelectors.basePackage("com.macro.mall.tiny.controller"))
               //为有@Api注解的Controller生成API文档
   //                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
               //为有@ApiOperation注解的方法生成API文档
   //                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
               .paths(PathSelectors.any())
               .build();
   }

   private ApiInfo apiInfo() {
       return new ApiInfoBuilder()
               .title("SwaggerUI演示")
               .description("mall-tiny")
               .contact("macro")
               .version("1.0")
               .build();
   }
   }
```



##### 四、和Swagger UI的集成

> 从github swagger-ui 上下载Swagger-UI, 把该项目dist目录下的内容拷贝到项目的resources的目录public下

##### 五、访问

>http://localhost:8080/swagger-ui.html




