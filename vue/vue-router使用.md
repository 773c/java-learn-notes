##### url的hash（默认使用这种）
> ---- F12 在console下输入location.hash = 'foo'即可使改变url不发生整体刷新

##### html5的history
> ---- F12 在console下输入history.pushState({},'','foo')

##### npm安装vue-router
> ---- npm install vue-router --save

##### 一、配置vue-router
index.js

````javascript
//配置路由信息
import VueRouter from 'vue-router'
import Vue from 'vue'

//1.通过Vue.use(插件)，安装插件
Vue.use(VueRouter)

//2.创建路由对象
const routes = [
    {
        path:'/home',
        component:home
    },{
        path:'/about',
        component:about
    }
]
const router = new VueRouter({
    //配置路由和组件的关系
    routes
    mode:'history'（默认是使用hash，这里使用history（默认使用pushState））
})

//3.将路由对象传入vue实例
export default router

*main.js*
import Vue from 'vue'
import App from './App.vue'
import router from './router/index'

Vue.config.productionTip = false

new Vue({
   render: h => h(App),
   router:router
}).$mount('#app')
````



##### 二、使用路由

````html
---- <router-link></router-link>
---- <router-view></router-view>
````

##### 三、路由懒加载（当项目过大都在一个js文件时会导致加载过慢，所以将各个组件分离）
> #将导入改为懒加载方式
> ---- const Home = () => import('组件路径')

##### 四、路由嵌套

> ---- chileren:[.....]（子组件path不用加/）

##### 五、路由导航实现跳转title变化
> #在路由的index.js下配置
> ---- router.beforeEach((to,from,next) => {
>      document.title = to.matched[0].meta.title（meta在index.js的routes组件中创建）
>      next()
> })

##### 六、保持组件的状态

> ---- <keep-alive></keep-alive>
> #使用了keep-alive，activated和deactivated就可以使用
> #activated：保持活跃
> #deactivated：不活跃
> #使用了keep-alive就不会频繁调用created和destroyed函数
> #如果需要某个组件频繁的被created和destoryed，就需要加上属性：
> ---- include="组件导出的name"
> ---- exclude="组件导出的name"