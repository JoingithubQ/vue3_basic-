<template>
  <div class="watchComp">
    <h2>watch监听</h2>
    case1：<input type="text" v-model="message">
    <hr>
    case2：<input type="text" v-model="message2">
    <hr>
    case3：<input type="text" v-model="message3.foo.bar.name">
    <hr>
    case4：<input type="text" v-model="message3.foo.bar.age">
  </div>
   
</template>

<script setup lang="ts">
import { ref,reactive,watch } from 'vue';
const message = ref<string>('hao');
const message2 = ref<string>('qiang');

// 监听单个数据源
// watch(message,(newVal,oldVal)=>{
//   console.log(newVal,oldVal);
// })
// 监听多个数据源
// watch([message,message2],(newVal,oldVal)=>{
//   console.log(newVal,oldVal);
// })

// ref 变成 reactive
const message3 = reactive({
  foo:{
    bar:{
      name:'小花',
      age:32
    }
  }
})
// 深度监听 开启watch的第三个配置 里面加上deep 值为true 或者 将ref 变成reactive(内置好了deep) 
// 直接监听复杂类型的变化 这样写 发现新值旧值都一样 只需要新值的话可以这样用 还需要旧值的话就要变成具体监听的值点进去比如message3.foo.bar.name
// watch(message3,(newVal,oldVal)=>{
//   console.log(newVal,oldVal);
// },{
//   // deep:true,//ref 需要 深度监听 reactive 不需要这个deep
// })

watch(()=>message3.foo.bar.name,(newVal,oldVal)=>{
  console.log(newVal,oldVal);
},{
  // deep:true,//深度监听
  immediate:true,//默认为false 作用：为true的话一进来就触发一次监听 立即执行一次
  flush:"pre",//pre: 组件更新之前调用 sync：同步执行  post 组件更新之后执行
})

watch(()=>message3.foo.bar.age,(newVal,oldVal)=>{
  console.log(newVal,oldVal);
})
</script>

<style scoped>
.watchComp{
  border: 2px solid red;
}
</style>
