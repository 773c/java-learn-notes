#### Vuex����һ��״̬����ģʽ���൱�ڽ����е����ݴ浽vuex���棩

#### **������**
##### һ��state���洢���ݵĵط����൱�������data��

#App.vue

<div id="app">
   <h2>{{$store.state.counter}}</h2>
</div>
#index.js��vuex��

````javascript
const store = new Vuex.Store({
   state:{
      counter:1000
   }
})
````

##### ����getters������������Ҫ����һϵ�б仯���൱�������computed��
#App.vue

<div id="app">
   <h2>{{$store.getters.powerCounter}}</h2>
</div>
#index.js��vuex��

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

##### ����mutations�����ں����ı�д�����޸�vuex������ʱ����ͨ��mutations����Ϊdevtools����ֻ���mutations���и��٣��൱�������methods��
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
            this.$store.commit('increment')   //commit����mutations���൱���ύ��increment����
         },
         subtraction(){
            this.$store.commit('decrement')
         }
      }
   }
</script>
#index.js��vuex��

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

##### �ġ�actions�����ڴ����첽����Ĳ�������Ϊ������첽������devtools���޷���׽mutations
#App.vue

<div id="app">
   <h2>{{$store.state.counter}}</h2>
   <button @click="updateInfo">�޸���Ϣ</button>
</div>
<script>
   export default {
      methods:{
         updateInfo(){
            this.$store.dispatch('aUpdateInfo')   //dispatch����actions
         }
      }
   }
</script>
#index.js��vuex��

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
   }��
   actions:{
      //context:������ �����൱��store
      aUpdateInfo(context){
         setTimeout(() => {
            context.commit('updateInfo')
         },1000)
      }
   }
})
````

��1�����첽������ɺ���θ���App.vue
#App.vue

<script>
   export default {
      methods:{
         updateInfo(){
            this.$store
	.dispatch('aUpdateInfo','������Ϣ')   //dispatch����actions
	.then(res => {
                  console.log('����������ύ')
                  console.log(res)
               })
         }
      }
   }
</script>
#index.js��vuex��

````javascript
  actions:{
      //context:������ �����൱��store
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

##### �塢modules�����ڴ�����ݵ�ģ��
#App.vue

<div id="app">
   <h2>{{$store.state.a.name}}</h2>   //module�Ὣ����ת�뵽state��
</div>
#index.js��vuex��

````javascript
//��������
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


