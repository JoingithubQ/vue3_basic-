<template>
    <div class="watcheffectComp">
        <h2>watcheffect高级监听器</h2>
        <input id="ipt" type="text" v-model="message">
        <input type="text" v-model="message2">
        <button @click="stopwatch">停止监听</button>
    </div>
</template>

<script setup lang="ts">
import { ref,watchEffect } from 'vue';
const message = ref<string>('飞机');
const message2 = ref<string>('飞机的杯子');

// 默认立即触发 
const stop = watchEffect((oninvalidate)=>{
    console.log('message=>>>',message.value);
    // console.log('message2=>>>',message2.value);
    // let ipt:HTMLInputElement = document.querySelector('#ipt') as HTMLInputElement;
    // console.log('dom没渲染拿不到加配置项即可',ipt);
    // 清除一些副作用 会先执行
    oninvalidate(()=>{
        console.log('before');
    })
},{
    flush:'post',
    onTrigger(e){//此配置可以做一些调试 
        debugger
    }
})
// 点击停止监听输入值就不会监听了
const stopwatch = ()=>{
    stop();
}
</script>

<style scoped>
.watcheffectComp{
    margin-top: 20px;
    border: 2px solid;
}
</style>