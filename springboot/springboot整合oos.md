#### 整合OSS实现文件上传

> 本文主要讲解mall整合OSS实现文件上传的过程，采用的是服务端签名后前端直传的方式。

#### 一、OOS

> 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。OSS可用于图片、音视频、日志等海量文件的存储。各种终端设备、Web网站程序、移动应用可以直接向OSS写入或读取数据。

##### （1）OOS中的相关概念

* Endpoint：访问域名，通过该域名可以访问OSS服务的API，进行文件上传、下载等操作。
* Bucket：存储空间，是存储对象的容器，所有存储对象都必须隶属于某个存储空间。
* Object：对象，对象是 OSS 存储数据的基本单元，也被称为 OSS 的文件。
* AccessKey：访问密钥，指的是访问身份验证中用到的 AccessKeyId 和 AccessKeySecret。

##### （2）OSS的相关设置

##### （2-1）开通OSS服务

- 登录阿里云官网；
- 将鼠标移至产品标签页，单击对象存储 OSS，打开OSS 产品详情页面；
- 在OSS产品详情页，单击立即开通。

##### （2-2）创建存储空间

* 点击网页右上角控制台按钮进入控制台

![image-20200506104819726](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200506104819726.png)

* 选择我的云产品中的对象存储OSS

![image-20200506104923996](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200506104923996.png)

* 点击左侧Bucket列表创建Bucket存储空间

![image-20200506105048302](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200506105048302.png)

* 新建存储空间并设置读写权限为公共读

![img](http://www.macrozheng.com/images/arch_screen_80.png)

##### （2-3）跨域资源共享（CORS）的设置

> 由于浏览器处于安全考虑，不允许跨域资源访问，所以我们要设置OSS的跨域资源共享。

* 选择一个存储空间，打开其权限设置

![image-20200506105413172](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200506105413172.png)

* 点击跨越设置的设置按钮

![image-20200506105427034](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200506105427034.png)

* 点击创建规则

![image-20200506105513608](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200506105513608.png)

* 进行跨域规则设置

![img](http://www.macrozheng.com/images/arch_screen_84.png)

#### 流程示例图

![img](http://www.macrozheng.com/images/arch_screen_85.png)

##### 流程介绍

1. Web前端请求应用服务器，获取上传所需参数（如OSS的accessKeyId、policy、callback等参数）
2. 应用服务器返回相关参数
3. Web前端直接向OSS服务发起上传文件请求
4. 等上传完成后OSS服务会回调应用服务器的回调接口
5. 应用服务器返回响应给OSS服务
6. OSS服务将应用服务器回调接口的内容返回给Web前端

##### `详解`

> 1. 首先创建`OssConfig`配置类连接阿里云OSS客户端Client，将`endpoint`（访问域名）、`accessKeyId`（身份验证的标识）、`accessKeySecret`（秘钥）通过Client构造传入
>
> 2. 其次创建`OssPolicy`（用于返回给前端的数据）、`OssCallbackParam`（会用上传成功告诉阿里云OSS回调的设置）、`OssCallback`（用于上传成功将图片的上传信息返回给前端）
>
> 3. 然后创建业务接口`OssService`并实现它。
>
>    3-1.  生成上传策略API，配置`PolicyConditions`（策略条件），配置文件的大小范围（PolicyConditions.`COND_CONTENT_LENGTH_RANGE`,0,fileSize）和文件存储路径前缀dirPrefix（`MatchMode.StartWith`,PolicyConditions.`COND_KEY`,dir）
>
>    3-2.  生成带期限的postPolicy（ossClient.`generatePostPolicy`(expirationDate,policyConditions)）
>
>    3-3.  将`postPolicy`进行加密
>
>    先转为byte类型（byte[] binaryData = postPolicy.getBytes("utf-8")），然后在生成加密字符串policy（BinaryUtil.toBase64String(binaryData)）
>
>    3-4.  对`policy`生成签名
>
>    String signature = ossClient.calculatePostSignature(postPolicy)
>
>    3-5.  将`callback`回调信息加密
>
>    String callbackData = BinaryUtil.toBase64String(JSONUtil.toJsonStr(ossCallbackParam).getBytes("utf-8"))
>
>    3-6.  将数据封装到`OssPolicy`中返回

#### 二、整合OSS实现文件上传

##### （1）在pom.xml中添加相关依赖

````java
<!-- OSS SDK 相关依赖 -->
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>2.5.0</version>
</dependency>
````

##### （2）修改SpringBoot配置文件application.yml

> 注意：endpoint、accessKeyId、accessKeySecret、bucketName、callback、prefix都要改为你自己帐号OSS相关的，callback需要是公网可以访问的地址。

````java
# OSS相关配置信息
aliyun:
  oss:
    endpoint: oss-cn-shenzhen.aliyuncs.com # oss对外服务的访问域名
    accessKeyId: test # 访问身份验证中用到用户标识
    accessKeySecret: test # 用户用于加密签名字符串和oss用来验证签名字符串的密钥
    bucketName: macro-oss # oss的存储空间
    policy:
      expire: 300 # 签名有效期(S)
    maxSize: 10 # 上传文件大小(M)
    callback: http://localhost:8080/aliyun/oss/callback # 文件上传成功后的回调地址
    dir:
      prefix: mall/images/ # 上传文件夹路径前缀
````

##### （3）添加OSS的相关Java配置

> 用于配置OSS的连接客户端OSSClient。

OssConfig

````java
import com.aliyun.oss.OSSClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 连接阿里云OSS配置类
 */
@Configuration
public class OssConfig {
    @Value("${aliyun.oss.endpoint}")
    private String endpoint;
    @Value("${aliyun.oss.accessKeyId}")
    private String accessKeyId;
    @Value("${aliyun.oss.accessKeySecret}")
    private String accessKeySecret;
    @Bean
    public OSSClient ossClient(){
        return new OSSClient(endpoint,accessKeyId,accessKeySecret);
    }
}
````

##### （4）添加OSS上传策略封装对象OssPolicy

> 前端直接上传文件时所需参数，从后端返回过来。

````java
import io.swagger.annotations.ApiModelProperty;

/**
 * 获取OSS上传文件授权返回结果
 */
public class OssPolicy implements Serializable{
    private static final long serialVersionUID = 1L;

    @ApiModelProperty(value = "访问身份验证用到的标识")
    private String accessKeyId;

    @ApiModelProperty(value = "用于表单上传的策略")
    private String policy;

    @ApiModelProperty(value = "对策略签名后的字符串")
    private String signature;

    @ApiModelProperty(value = "存储路径前缀")
    private String dir;

    @ApiModelProperty(value = "OSS访问域名")
    private String host;

    @ApiModelProperty(value = "上传成功回调的设置")
    private String callback;


    //省略了所有getter,setter方法
}
````

##### （5）添加OSS上传成功后的回调参数对象OssCallbackParam

> 当OSS上传成功后，会根据该配置参数来回调对应接口。

````java
import io.swagger.annotations.ApiModelProperty;

/**
 * oss上传成功后的回调参数
 */
public class OssCallbackParam implements Serializable {
    private static final long SerialVersionUID = 1L;

    @ApiModelProperty(value = "回调成功的地址")
    private String callbackUrl;

    @ApiModelProperty(value = "回调时传入request的参数")
    private String callbackBody;

    @ApiModelProperty(value = "回调时传入参数的类型，比如表单提交类型")
    private String callbackBodyType;

    //省略了所有getter,setter方法
}
````

##### （6）添加OSS上传成功后的回调结果对象OssCallback

> 回调接口中返回的数据对象，封装了上传文件的信息。

````java
import io.swagger.annotations.ApiModelProperty;

/**
 * oss上传文件的回调结果
 */
public class OssPolicy implements Serializable{
    private static final long serialVersionUID = 1L;

    @ApiModelProperty(value = "访问身份验证用到的标识")
    private String accessKeyId;

    @ApiModelProperty(value = "用于表单上传的策略")
    private String policy;

    @ApiModelProperty(value = "对策略签名后的字符串")
    private String signature;

    @ApiModelProperty(value = "存储路径前缀")
    private String dir;

    @ApiModelProperty(value = "OSS访问域名")
    private String host;

    @ApiModelProperty(value = "上传成功回调的设置")
    private String callback;

    //省略了所有getter,setter方法
}
````

##### （7）添加OSS业务接口OssService

````java
/**
 * oss上传管理Service
 */
public interface OssService {
    /**
     * oss上传策略生成
     */
    OssPolicy policy();

    /**
     * oss上传成功回调
     */
    OssCallback callback(HttpServletRequest request);
}
````

##### （8）添加OSS业务接口OssService的实现类OssServiceImpl

````java
/**
 * oss上传管理Service实现类
 */
@Service
public class OssServiceImpl implements OssService {
    private static final Logger LOGGER = LoggerFactory.getLogger(OssServiceImpl.class);

    @Value("${aliyun.oss.endpoint}")
    private String endpoint;
    @Value("${aliyun.oss.bucketName}")
    private String bucketName;
    @Value("${aliyun.oss.policy.expire}")
    private long expire;
    @Value("${aliyun.oss.maxSize}")
    private Integer maxSize;
    @Value("${aliyun.oss.callback}")
    private String callback;
    @Value("${aliyun.oss.dir.prefix}")
    private String dirPrefix;

    @Autowired
    private OSSClient ossClient;

    @Override
    public OssPolicy policy() {
        OssPolicy ossPolicy = new OssPolicy();
        //存储目录
        String dir = dirPrefix+ DateUtil.format(new Date(),"yyyyMMdd");
        //签名有效期
        long expirationTime = System.currentTimeMillis() + expire * 1000;
        Date expirationDate = new Date(expirationTime);
        //文件大小（将M转为B）
        long fileSize = maxSize * 1024 * 1024;
        //回调设置
        OssCallbackParam ossCallbackParam = new OssCallbackParam();
        ossCallbackParam.setCallbackUrl(callback);
        ossCallbackParam.setCallbackBody("filename=${object}&size=${size}&mimeType=${mimeType}&height=${imageInfo.height}&width=${imageInfo.width}");
        ossCallbackParam.setCallbackBodyType("application/x-www-form-urlencoded");
        //提交节点域名
        String host = "http://" + bucketName + "." + endpoint;
        try{
            PolicyConditions policyConditions = new PolicyConditions();
            //设置文件大小
            policyConditions.addConditionItem(PolicyConditions.COND_CONTENT_LENGTH_RANGE,0,maxSize);
            //设置文件存储路径前缀
            policyConditions.addConditionItem(MatchMode.StartWith,PolicyConditions.COND_KEY,dir);
            //获取postPolicy
            String postPolicy = ossClient.generatePostPolicy(expirationDate,policyConditions);
            //转换为二进制数据
            byte[] binaryData = postPolicy.getBytes("utf-8");
            //将二进制数据转换为加密字符串
            String policy = BinaryUtil.toBase64String(binaryData);
            //对policy生成签名
            String signature = ossClient.calculatePostSignature(postPolicy);
            //生成加密回调
            String callbackData = BinaryUtil.toBase64String(JSONUtil.toJsonStr(ossCallbackParam).getBytes("utf-8"));
            //返回结果
            ossPolicy.setAccessKeyId(ossClient.getCredentialsProvider().getCredentials().getAccessKeyId());
            ossPolicy.setPolicy(policy);
            ossPolicy.setSignature(signature);
            ossPolicy.setHost(host);
            ossPolicy.setDir(dir);
            ossPolicy.setCallback(callbackData);
        }catch (Exception e){
            LOGGER.error("签名生成失败",e);
        }
        return ossPolicy;
    }

    @Override
    public OssCallback callback(HttpServletRequest request) {
        OssCallback ossCallback= new OssCallback();
        String filename = request.getParameter("filename");
        filename = "http://".concat(ALIYUN_OSS_BUCKET_NAME).concat(".").concat(ALIYUN_OSS_ENDPOINT).concat("/").concat(filename);
        ossCallback.setFilename(filename);
        ossCallback.setSize(request.getParameter("size"));
        ossCallback.setMimeType(request.getParameter("mimeType"));
        ossCallback.setWidth(request.getParameter("width"));
        ossCallback.setHeight(request.getParameter("height"));
        return ossCallback;
    }

}
````

##### （9）添加OssController定义接口

````java
/**
 * Oss相关操作接口
 */
@RestController
@Api(tags = "OssController", description = "Oss管理")
@RequestMapping("/aliyun/oss")
public class OssController {
     @Autowired
    private OssService ossService;

    @ApiOperation(value = "oss上传签名生成")
    @RequestMapping(value = "/policy", method = RequestMethod.GET)
    public ResponseResultUtil policy() {
        OssPolicy ossPolicy = ossService.policy();
        return ResponseResultUtil.success(ossPolicy);
    }

    @ApiOperation(value = "oss上传成功回调")
    @RequestMapping(value = "callback", method = RequestMethod.POST)
    public ResponseResultUtil callback(HttpServletRequest request) {
        OssCallback ossCallback = ossService.callback(request);
        return ResponseResultUtil.success(ossCallback);
    }
}
````