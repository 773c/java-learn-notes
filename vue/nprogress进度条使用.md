##### 一、安装

> ① npm install nprogress --save 
> ② yarn add nprogress

##### 二、用法

````javascript
NProgress.start(); //进度条出现
NProgress.done();  //进度条消失
````

##### 三、路由静态页面跳转

##### （1）在`main.js`中导入

````javascript
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'
````

##### （2）使用

> 一般这里可以用来做权限跳转，任何路由的跳转都判断token是否有效，有效则从服务器获取用户信息，无效则跳转到登录页面。

````javascript
router.beforeEach((to, from, next) => {
　//出现进度条
  NProgress.start()
  next()
})

router.afterEach(() => {
  //进度条消失
  NProgress.done()
})
````

##### 四、异步网络请求

##### （1）在`request.js`中导入

````javascript
import NProgress from 'nprogress' 
import 'nprogress/nprogress.css'
````

##### （2）使用

````javascript
//请求拦截器
axios.interceptors.request.use(function (config) {
　　//出现进度条
    NProgress.start()return config
}, function (error) {
    // Do something with request error
    return Promise.reject(error)
})

//响应拦截器中
axios.interceptors.response.use(config => {
    //隐藏进度条
    NProgress.done()
    return config
})
````

##### 五、NProgress配置

##### （1）showSpinner：进度环显示隐藏

````javascript
NProgress.configure({showSpinner: false});
````

##### （2）ease：调整动画设置，ease可传递CSS3缓冲动画字符串（如ease、linear 等）。speed为动画速度（单位ms）

````javascript
NProgress.configure({ease:'ease',speed:500});
````

##### （3）minimum：最低百分比

````javascript
NProgress.configure({minimum:0.3});
````

##### （4）百分比：通过设置progress的百分比，调用 .set(n)来控制进度，其中n的取值范围为0-1

````javascript
NProgress.set(0.4);
````

##### 六、修改进度条颜色

在`App.vue`中的`style`中增加：

````css
#nprogress .bar {
      background: red !important; //自定义颜色
 }
````

