##### url��hash��Ĭ��ʹ�����֣�
> ---- F12 ��console������location.hash = 'foo'����ʹ�ı�url����������ˢ��

##### html5��history
> ---- F12 ��console������history.pushState({},'','foo')

##### npm��װvue-router
> ---- npm install vue-router --save

##### һ������vue-router
index.js

````javascript
//����·����Ϣ
import VueRouter from 'vue-router'
import Vue from 'vue'

//1.ͨ��Vue.use(���)����װ���
Vue.use(VueRouter)

//2.����·�ɶ���
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
    //����·�ɺ�����Ĺ�ϵ
    routes
    mode:'history'��Ĭ����ʹ��hash������ʹ��history��Ĭ��ʹ��pushState����
})

//3.��·�ɶ�����vueʵ��
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



##### ����ʹ��·��

````html
---- <router-link></router-link>
---- <router-view></router-view>
````

##### ����·�������أ�����Ŀ������һ��js�ļ�ʱ�ᵼ�¼��ع��������Խ�����������룩
> #�������Ϊ�����ط�ʽ
> ---- const Home = () => import('���·��')

##### �ġ�·��Ƕ��

> ---- chileren:[.....]�������path���ü�/��

##### �塢·�ɵ���ʵ����תtitle�仯
> #��·�ɵ�index.js������
> ---- router.beforeEach((to,from,next) => {
>      document.title = to.matched[0].meta.title��meta��index.js��routes����д�����
>      next()
> })

##### �������������״̬

> ---- <keep-alive></keep-alive>
> #ʹ����keep-alive��activated��deactivated�Ϳ���ʹ��
> #activated�����ֻ�Ծ
> #deactivated������Ծ
> #ʹ����keep-alive�Ͳ���Ƶ������created��destroyed����
> #�����Ҫĳ�����Ƶ���ı�created��destoryed������Ҫ�������ԣ�
> ---- include="���������name"
> ---- exclude="���������name"