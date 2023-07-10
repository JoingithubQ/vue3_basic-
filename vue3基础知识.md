## vue3基础知识 => 进阶 

​	https://www.bilibili.com/video/BV1dS4y1y7vd/?p=9&spm_id_from=pageDriver&vd_source=e000cf9a0b106037a07683868555c299

1. ref 是将数据响应式（深层） isRef是判断是否是ref数据  shallowRef是浅层数据响应式 只能响应到value

   2.customRef 是自定义ref 就是把ref的底层实现交给用户去自定义

3. ref(深层) 与 shallowRef(浅层次响应式)

  4.ref和shallowRef 是不能写在一起的不然 会影响 shallowRef 造成试图更新 为啥？

​        原因 ：triggerRef(a22);//强制刷新 也是 shallowRef 的底层

 5.ref也可以获取dom元素

 6.ref 支持所有类型           reactive 仅支持引用类型 Array Object Map Set 

 7.ref 取值 赋值 都需要加.value ····   reactive不需要.value

 8.reactive([])数组 proxy 只能通过数组方法却不能直接赋值 否则会破坏响应式对象

​    解决 1.数据可以使用push 加结构

​    解决 2.将let arr1 = reactive<string[]>([]); 变为let arr1 = reactive<{arr:string[]}>({arr:[]});

 9.readonly 只读属性

```javascript
   let list = reactive({name:'haoqiang'});
   let read = readonly(list);//readonly 只读属性
   read.name = 'qweq';//无法为“name”赋值，因为它是只读属性
   const show = () =>{
      // read.name = 'qweq'; // 不能赋值 read只读
      list.name = '大满';
      // read 受原始数据的变化而变化 但是单独改变 read 则不行
      console.log(list,read)
   }
```

10.shallowReactive      shallowReactive 也是浅层的 同样和ref一样也会受 reactive 的影响 被迫改值在同时使用的过程中

####  to 全家桶

 1.toRef  `ref`用于创建新的响应式引用对象，而`toref`用于将已存在的响应式对象转换为可绑定的引用  

​    `toRef`函数接收两个参数：一个响应式对象和该对象中某个属性的键名。

​    它返回一个新的响应式引用，可以通过`.value`属性访问和修改该属性的值。

```javascript
import { reactive, toRef } from 'vue';

const state = reactive({
  count: 0,
  message: 'Hello'
});

const countRef = toRef(state, 'count');
console.log(countRef.value); // 输出 0
countRef.value++; // 修改值为 1
console.log(state.count); // 输出 1
```

2.toRefs  将结构出来的数据进行响应式

  `toRefs`函数接收一个响应式对象，并将该对象的所有属性转化为普通引用对象。

  它返回一个包含所有转化后引用的对象，每个属性都可以通过`.value`属性访问和修改

   **底层实现**：

<img src="C:\Users\h1045905457_25831\AppData\Roaming\Typora\typora-user-images\image-20230707153613632.png" alt="image-20230707153613632" style="zoom:100%;" />

```javascript
import { reactive, toRefs } from 'vue';

const state = reactive({
  count: 0,
  message: 'Hello'
});

const refs = toRefs(state);
console.log(refs.count.value); // 输出 0
refs.count.value++; // 修改值为 1
console.log(state.count); // 输出 1
```

总结起来，`toRef`用于将响应式对象中的单个属性转化为响应式引用，而`toRefs`用于将整个响应式对象的所有属性都转化为普通引用对象

3.toRaw  把 man 响应式数据变成 普通数据结构 {name: '小满', age: 23, like: 'JK'} 

​	const man = reactive({name:'小满',age:23,like:'JK'})

​	console.log(man,toRaw(man));  // {name: '小满', age: 23, like: 'JK'}

​	**底层实现：**

​      就一行代码 源码中提供一个变量   __v_raw 

​	  console.log(man,man['__v_raw'])   ===  console.log(man,toRaw(man));

4.computed 计算属性 计算属性本身式响应式数据 取值要.value 如同 ref

```javascript
 const name = ref('张');
 const texts = ref('三');

// 1. 选项式写法  支持一个对现象传入get函数以及set函数自定义操作
 const changenames = () =>{
     names.value = '小-满';
 }
 let names = computed<string>({
     get(){//读取值
         return name.value + '-' + texts.value
     },
     set(newVal){//设置值
        console.log(newVal.split('-'));
         [name.value,texts.value] = newVal.split('-');
     }
 }) 


// 2. 函数式写法  只能支持一个getter函数 不允许修改值的
 let names = computed(()=>{
     return name.value + '-' + texts.value;
 })
 
 
// demo
import { computed, reactive } from 'vue';

const state = reactive({
  num1: 5,
  num2: 10,
});

const sum = computed(() => {
  return state.num1 + state.num2;
});

console.log(sum.value); // 输出 15

state.num1 = 7;
console.log(sum.value); // 输出 17

state.num2 = 20;
console.log(sum.value); // 输出 27
```

当`state.num1`或`state.num2`发生变化时，`sum`的值会自动更新。在示例的后续代码中，我们修改了`state.num1`和`state.num2`的值，并打印出了更新后的`sum.value`。

5.watch  监听 响应式数据的变化 被 ref reactive 包裹的数据才可以用watch监听 

```javascript
// 监听单个数据源
 watch(message,(newVal,oldVal)=>{
   console.log(newVal,oldVal);
 })
// 监听多个数据源
 watch([message,message2],(newVal,oldVal)=>{
  console.log(newVal,oldVal);
 })
 
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
```

6.watchEffect 高级监听

```
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
```

