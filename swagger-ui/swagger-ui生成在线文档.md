#### ˵����Swagger-UI��HTML, Javascript, CSS��һ�����ϣ����Զ�̬�ظ���ע����������API�ĵ���

##### һ������ע��

* @Api����������Controller�࣬����Controller����ĵ���Ϣ
* @ApiOperation����������Controller���еķ��������ɽӿڷ�������ĵ���Ϣ
* @ApiParam���������νӿ��еĲ��������ɽӿڲ�������ĵ���Ϣ
* @ApiModelProperty����������ʵ��������ԣ���ʵ��������������򷵻ؽ��ʱ��ֱ����������ĵ���Ϣ

���ӣ�

```
@Api(tags = "PmsBrandController", description = "��ƷƷ�ƹ���")��
@ApiOperation("��ȡ����Ʒ���б�")
```

##### ����spring����swagger-ui

```
<!--Swagger-UI API�ĵ���������-->
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

##### �������Swagger-UI������
ע�⣺Swagger������API�ĵ��ķ�Χ�����ֲ�ͬ��ѡ��

> ��1������ָ������������API�ĵ�
> ��2��������ָ��ע������API�ĵ�
> ��3��������ָ��ע��ķ�����API�ĵ�

���룺

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

 * Swagger2API�ĵ�������
   */
   @Configuration
   @EnableSwagger2
   public class Swagger2Config {
   @Bean
   public Docket createRestApi(){
       return new Docket(DocumentationType.SWAGGER_2)
               .apiInfo(apiInfo())
               .select()
               //Ϊ��ǰ����controller����API�ĵ�
               .apis(RequestHandlerSelectors.basePackage("com.macro.mall.tiny.controller"))
               //Ϊ��@Apiע���Controller����API�ĵ�
   //                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
               //Ϊ��@ApiOperationע��ķ�������API�ĵ�
   //                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
               .paths(PathSelectors.any())
               .build();
   }

   private ApiInfo apiInfo() {
       return new ApiInfoBuilder()
               .title("SwaggerUI��ʾ")
               .description("mall-tiny")
               .contact("macro")
               .version("1.0")
               .build();
   }
   }
```



##### �ġ���Swagger UI�ļ���

> ��github swagger-ui ������Swagger-UI, �Ѹ���ĿdistĿ¼�µ����ݿ�������Ŀ��resources��Ŀ¼public��

##### �塢����

>http://localhost:8080/swagger-ui.html




