##### 一、@PathVariable

> 当使用@RequestMapping URI template 样式映射时， 即 someUrl/{paramId}, 这时的paramId可通过 @Pathvariable注解绑定它传过来的值到方法的参数上。

示例代码：

````java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {
    
	@RequestMapping("/pets/{petId}")
	public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model 	model) {
		// implementation omitted
	}
}
````

注：上面代码把URI template 中变量 ownerId的值和petId的值，绑定到方法的参数上。若方法参数名称和需要绑定的uri template中变量名称不一致，需要在@PathVariable("name")指定uri template中的名称。

##### 二、@RequestHeader

> @RequestHeader 注解，可以把Request请求header部分的值绑定到方法的参数上。

这是一个Request 的header部分：

````bash
Host localhost:8080
Accept text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding gzip,deflate
Accept-Charset ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive 300
````

java代码：

````java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding,
@RequestHeader("Keep-Alive") long keepAlive) {
}
````

注：上面的代码，把request header部分的 Accept-Encoding的值，绑定到参数encoding上了， Keep-Alive header的值绑定到参数keepAlive上。

##### 三、@CookieValue

> 可以把Request header中关于cookie的值绑定到方法的参数上。

例如有如下Cookie值：

````bash
JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
````

java代码：

````java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie) {
}
````

##### 四、RequestParam

> （1）常用来处理简单类型的绑定，通过Request.getParameter() 获取的String可直接转换为简单类型的情况（ String--> 简单类型的转换操作由ConversionService配置的转换器来完成）；因为使用request.getParameter()方式获取参数，所以可以处理get 方式中queryString的值，也可以处理post方式中 body data的值
>
> （2）用来处理Content-Type: 为 application/x-www-form-urlencoded编码的内容，提交方式GET、POST
>
> （3）该注解有两个属性： value、required； value用来指定要传入值的id名称，required用来指示参数是否必须绑定（required默认为true）

##### 五、RequestBody

> （1）该注解常用来处理Content-Type: 不是application/x-www-form-urlencoded编码的内容，例如application/json, application/xml等
>
> （2）它是通过使用HandlerAdapter 配置的HttpMessageConverters来解析post data body，然后绑定到相应的bean上的
>
> （3）因为配置有FormHttpMessageConverter，所以也可以用来处理 application/x-www-form-urlencoded的内容，处理完的结果放在一个MultiValueMap<String, String>里，这种情况在某些特殊需求下使用，详情查看FormHttpMessageConverter api