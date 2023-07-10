<template>
  <HelloWorld/>
  <WatchEffect/>
  <div ref="div">{{str}}</div>
  <button @click="updata">更新数据</button>
</template>

<script setup lang="ts">
import { ref,onBeforeMount,onMounted,onBeforeUpdate,onUpdated,onBeforeUnmount,onUnmounted } from 'vue'
import HelloWorld from './components/HelloWorld.vue';
import WatchEffect from './components/WatchEffect.vue';
const str = ref<string>('dom');
const div = ref<HTMLDivElement>();
const updata = () =>{
  str.value = 'haoqiang';
}
// beforeCreate created setup 语法糖是没有这两个生命周期的 setup代替
// onBeforeMount 读不到dom onMounted 可以
// onBeforeUpdate 获取更新之前的dom  onUpdated 获取更新之后的dom
console.log('setup');
// 创建前
onBeforeMount(()=>{
  console.log('组件挂载前',div.value);
})
// 创建完成 
onMounted(()=>{
  console.log('组件挂载完成',div.value);
})
// 更新组件之前
onBeforeUpdate(()=>{
  // 可以做一些卸载的操作
  console.log('组件更新前',div.value?.innerHTML);
})
// 更新完成
onUpdated(()=>{
  console.log('组件更新完成',div.value?.innerHTML);
})
// 卸载之前
onBeforeUnmount(()=>{
  console.log('组件卸载前');
})
// 卸载完成
onUnmounted(()=>{
  console.log('组件卸载完成');
})
</script>

<style scoped>

</style>
