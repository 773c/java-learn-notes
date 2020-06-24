#### Vuex：是一个状态管理模式（相当于将公有的数据存到vuex里面）

#### **五大核心**
##### 一、state：存储数据的地方（相当于组件的data）

#App.vue

<div id="app">
   <h2>{{$store.state.counter}}</h2>
</div>
#index.js（vuex）

````javascript
const store = new Vuex.Store({
   state:{
      counter:1000
   }
})
````

##### 二、getters：用于数据需要经过一系列变化（相当于组件的computed）
#App.vue

<div id="app">
   <h2>{{$store.getters.powerCounter}}</h2>
</div>
#index.js（vuex）

````javascript
const store = new Vuex.Store({
   state:{
      counter:1000
   },
   getters:{
      powerCounter(state){
         return state.counter * state.counter
      }
   }
})
````

##### 三、mutations：用于函数的编写，当修改vuex的数据时必须通过mutations，因为devtools工具只会对mutations进行跟踪（相当于组件的methods）
#App.vue

<div id="app">
   <h2>{{$store.state.counter}}</h2>
   <button @click="addition">+</button>
   <button @click="subtraction">-</button>
</div>
<script>
   export default {
      methods:{
         addition(){
            this.$store.commit('increment')   //commit用于mutations，相当于提交到increment函数
         },
         subtraction(){
            this.$store.commit('decrement')
         }
      }
   }
</script>
#index.js（vuex）

````javascript
const store = new Vuex.Store({
   state:{
      counter:1000
   },
   getters:{
      powerCounter(state){
         return state.counter * state.counter
    },
   mutations:{
      increment(state){
         state.counter++
      },
      decrement(state){
         state.counter--
      }
   }
})
````

##### 四、actions：用于处理异步请求的操作，因为如果是异步操作，devtools就无法捕捉mutations
#App.vue

<div id="app">
   <h2>{{$store.state.counter}}</h2>
   <button @click="updateInfo">修改信息</button>
</div>
<script>
   export default {
      methods:{
         updateInfo(){
            this.$store.dispatch('aUpdateInfo')   //dispatch用于actions
         }
      }
   }
</script>
#index.js（vuex）

````javascript
const store = new Vuex.Store({
   state:{
      counter:1000
      info:{
         name:'cjj',
         age:23
      }
   },
   getters:{
      powerCounter(state){
         return state.counter * state.counter
    },
   mutations:{
      increment(state){
         state.counter++
      },
      decrement(state){
         state.counter--
      },
      updateInfo(state){
         state.info.name='qqf'
      }
   }，
   actions:{
      //context:上下文 这里相当于store
      aUpdateInfo(context){
         setTimeout(() => {
            context.commit('updateInfo')
         },1000)
      }
   }
})
````

例1：当异步操作完成后如何告诉App.vue
#App.vue

<script>
   export default {
      methods:{
         updateInfo(){
            this.$store
	.dispatch('aUpdateInfo','我是信息')   //dispatch用于actions
	.then(res => {
                  console.log('里面完成了提交')
                  console.log(res)
               })
         }
      }
   }
</script>
#index.js（vuex）

````javascript
  actions:{
      //context:上下文 这里相当于store
      aUpdateInfo(context,payload){
         return new Promise((resolve,reject) => {
            setTimeout(() => {
               context.commit('updateInfo')
               console.log(payload)
               resolve('6666')
            },1000)
         })
      }
   }
````

##### 五、modules：用于存放数据的模块
#App.vue

<div id="app">
   <h2>{{$store.state.a.name}}</h2>   //module会将数据转入到state中
</div>
#index.js（vuex）

````javascript
//创建对象
const moduleA = {
   state:{
      a:{
         name:'cjj'
         age:23
      }
   },
   mutations:{

   },
   actions:{

   },
   getters:{

   }
}

const store = new Vuex.Store({
   modules:{
      a:moduleA
   }
})
````


