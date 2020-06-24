##### @RequestParam

* 用来处理Content-Type: 为 application/x-www-form-urlencoded编码的内容。（Http协议中，如果不指定Content-Type，则默认传递的参数就是application/x-www-form-urlencoded类型）

* @RequestParam 底层是通过request.getParameter方式获得参数的，换句话说，@RequestParam 和request.getParameter是同一回事。因为使用request.getParameter()方式获取参数，可以处理get 方式中queryString的值，也可以处理post方式中 body data的值。

##### @RequestBody

* 处理HttpEntity传递过来的数据，一般用来处理非Content-Type: application/x-www-form-urlencoded编码格式的数据。如（application/json或者text/json）
* GET请求中，因为没有HttpEntity，所以@RequestBody并不适用。
* POST请求中，通过HttpEntity传递的参数，必须要在请求头中声明数据的类型Content-Type，SpringMVC通过使用HandlerAdapter 配置的HttpMessageConverters来解析HttpEntity中的数据，然后绑定到相应的bean上。

**GET请求可以使用@RequestBody来接收参数吗？**

> 答案是可以的。
>
> 为什么会这样呢？感觉要怀疑人生了，GET与@RequestParam，POST与@RequestBody才是我们映像中的绝配。
>
> 那么在什么情况下可以配合使用呢？需要两个条件，一是请求方式为GET，二是请求参数写入请求体中。即接口需要被上层的服务调用而非页面直接访问。由于目前微服务的运用越来越多，所以一般像这样的情况在实际的开发中会变得常见。
>
> 像这样的接口如何测试呢？可以使用curl命令，事例如下：curl -XGET -H "Content-Type:application/json" "http://host:port/requestmapping" -d '{"paramId":[1,2,3]}'

##### 总结

* 前端请求传Json对象则后端使用@RequestParam；
* 前端请求传Json对象的字符串则后端使用@RequestBody。