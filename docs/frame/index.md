## Vue通用系列
### 指令
```
#指令:是vue中以"v-"前缀开头的特殊属性，属性值通常是表达式（v-for/v-on/v-slot除外0）
它的作用是在属性值变化时，响应地更新DOM。
##指令的使用场景
-------------------------------------------
@指令+[参数]+表达式
@指令+[参数]+[修饰符]+事件处理程序
--------------------------------------------
#v-bind 值绑定：
##单值绑定:键值对的形式
<div :id='id'></div>
##多值绑定:省略键的形式
<div v-bind='{name:"test",age:"12"}'></div>
-------------------------------------------------
#表达式
##模版内只支持单一表达式。且表达式只能访问有限的全局访问对象（表达式沙盒化）
--------------------------------------------------

## 指令的参数(指令后面以冒号分割的就是参数) 
### 固定参数
	v-bind:id ="id" / v-on:click="handleClick" 

### 动态参数,动态参数中表达式的值应当是一个字符串，或者是 null.
	v-bind:[aName]='id' / v-on:[eName]='handler'
---------------------------------------------------
## 指令的修饰符,指令参数后面以'.'分割的就是指令修饰符
@click.stop='handleClick'

## 修饰符类型
事件修饰符
按键修饰符
鼠标按键修饰符
-------------------------------------------------------
如何在事件处理程序中，访问原生DOM事件
一 使用特殊的事件对象变量$event
如：<div @click="handleClick($event)"></div>

二 使用event箭头函数传入到事件处理程序

如：<div @click="(event)=>handleClick(event)"></div>

```
### 计算属性/方法/侦听器
```
计算属性:基于现有的响应式依赖进行缓存，基于已有的响应式数据生成的响应数据。默认只读。
计算属性默认的是提供一个getter方法，返回一个只读的值。
-----------------------------------------------------
computed
vue2 完整写法: object options写法
@缩略写法，默认提供只读属性,函数写法。对象>函数
computed:{
	fullName(){
		return this.firstName +'' + this.lastName;
	}
},
@完整写法，提供存取属性。对象写法。对象>对象
computed:{
	fullName:{
		get(){
			return this.firstName +'' + this.lastName;
		},
		set(newValue){
			const names = newValue.split(' ');
			this.firstName = names[0];
			this.lastName = names[names.length - 1];
		}
	}
}
------------------------------------------------------
vue3完整写法: 工厂函数写法。
import { computed } from 'vue';
@缩略写法，默认只提供只读属性。入参是函数，函数>函数
const fullName = computed(()=>{
	return this.firstName +'' + this.lastName;
})
@完整写法，提供存取属性。入参是对象.函数>对象
const fullName = computed({
	get(){
		return this.firstName +'' + this.lastName;
	},
	set(newValue){
		const names = newValue.split(' ');
		this.firstName = names[0];
		this.lastName = names[names.length - 1];
	}
})

#小结:
@计算属性惰性的，基于响应式依赖进行缓存。
@计算属性的缩略写法内层构成是函数，完整写法内层构成是对象。函数是缩略写法，对象是完整写法。
-------------------------------------------------------------------------------------------

```
### 类和样式属性绑定（特殊属性绑定）。
```
前言:属性名和属性值存在一对多绑定的机制，决定了属性值是由对象/数组/对象+数组形式的实现机制。
类属性绑定:对象语法/数组语法/对象+数组语法。
<div :class="{ active: isActive, 'text-danger': hasError }"></div>
<div :class="[activeClass, errorClass]"></div>
<div :class="[{ active: isActive }, errorClass]"></div>	
-------------------------------------------------
样式属性绑定:对象语法/数组语法/对象+数组语法。
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
<div :style="[baseStyles, overridingStyles]"></div>
<div :style="[{ color: activeColor }, overridingStyles]"></div>
-------------------------------------------------
#小结:
@类和样式属性绑定，是基于对象/数组/对象+数组的形式实现的。
@需要特别留意的是，class静态属性名值对可以和:class动态属性名值对共存，他们在渲染时会合并汇总在一起。
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }">
</div>
@在组件使用class属性
一 当组件只有一个根元素，class属性会被合并汇总到根元素上
```
![单根组件class属性合并规则](image-6.png)

```
二 当组件有多个根元素,class属性需要在组件内部使用$attrs.class来进行显式地接收
```
![class属性接收规则](image-7.png)

### 常用指令v-if/v-show || v-for
```
前言:
@一个指令的任务是在其表达式的值变化时响应式地更新 DOM,指令必须依附于一个元素，作为元素的特殊属性而存在。
@template容器支持v-if属性，不支持v-show属性，template容器也支持v-for属性
@当v-if和v-for同时出现在同一个元素上时，v-if具有更高的优先级。会首先被执行。
------------------------------------------------------------
# v-for 
@v-for:用来遍历数组或对象结构的数据类型,  (dataItem) in soureData
dataItem in sourceData 前后的参数分别是具体数据项和源数据项。
数据项参数的第一个参数一定是数据项的别名，最后一个参数一定是索引。注意别名参数支持解构的写法!
遍历数组时，数据项可接受两个参数,分别是别名和索引。
遍历对象时，数据项可接受三个参数,分别是别名,键名和索引。

v-for在遍历数据时，为什么要使用key值?
key值可以管理数据的状态（类似有记忆的能力）key值只能是基础类型，不能是对象。
key值可以避免重复渲染，方便在数据更新时，重用或重排现有的元素。从而实现提高页面性能和渲染稳定性。
默认不使用key值时，采用了就地更新的策略，数据更新时，数据项只和之前的索引关联，不会重新调整，容易出现渲染混乱的现象。

!!使用template作为v-for指令的容器时，key值要绑定到template元素上。
---------------------------------------------------------------------
数组的变更方法值得是会改变源数组的方法。
@数组的变更方法
后进后出方法:push/pop
前进前出方法:unshift/shift
排序和倒置方法:sort/reverse
增删改方法:splice


@数组的非变更方法:不改变源数组，调用后返回了新的数组
----------------------------------------------------------

```
![v-if/v-show 特点总结](image-8.png)<br/>
![v-for和v-if需要一起使用时](image-9.png)<br/>

### 常用指令v-model与表单元素
![v-model与表单元素](image-10.png)<br/>
```
v-model内部做了属性绑定和事件处理更新属性，是一个会自动适配表单元素选择对应属性和事件绑定处理程序的vue语法糖。
```
### 监听器 watch
vue3监听器<br/>
![vue3监听器](image-13.png)<br/>
![vue3深度监听](image-14.png)<br/>
![vue3即时回调监听](image-15.png)<br/>
![vue3 watchEffect语法糖，监听源和回调使用了监听源数据的缩略写法](image-16.png)<br/>
![vue3 DOM更新后执行回调，watchPostEffect语法糖](image-17.png)<br/>
```
监听器的构成要素:监听的源数据，源数据变化触发的回调函数，监听器的配置项,配置回调函数的执行时机或行为。
监听器 = 响应式源数据 + 响应状态变更回调 + 控制监听行为/回调函数的执行时机或行为的监听器配置项（深度监听/立即执行/DOM更新后执行）。
------------------------------------------------------------------------
vue2中监听器的写法,配置项的形式
vue2中监听的源数据和源数据变化的回调函数是一体化的写法。
监听器配置项,控制监听器行为,回调函数行为:深度监听源数据/立即执行回调函数/DOM更新之后执行回调函数
watch:{
	obj:{//源数据
		handler(newValue,oldValue){//源数据变化回到
			console.log(newValue,oldValue);
		},
		deep:true,
		immediate:true,
		flush:'post'
		//默认情况下,响应状态变更时，如果这个响应状态又是被监听的源数据，那么会执行DOM跟新和状态变更回调。状态变更回调
	    //会先执行，此时如果在状态变更回调中访问了DOM的话，这个DOM是没有更新的DOM，使用flush: 'post'选项，会在DOM更新
		//之后再执行状态变更回调，回调中将访问到更新后的DOM.
	
	},
	num2(newValue,oldValue){
		console.log(newValue,oldValue);
	}
}
------------------------------------------------------------------------
vue3中监听器的写法,工厂函数式写法(组合式写法)
vue3中监听器的构成要素是作为工厂函数的不同入参传入的。
import {ref, watch } from 'vue'


--------------------------------------------------------------------------
@监听器的销毁
注意,默认监听器是不需要主动卸载的，组件卸载时会自动销毁监听器。
如果需要主动销毁监听器，需要使用内置钩子的形式创建监听器，再在合适时机进行监听器创建结果的unwatch卸载的操作。
----------------------------------------------------------------------------
vue2手动卸载操作
const unwatch = this.$watch('foo', callback)
unwatch()
------------------------------------------------------------------------------
vue3手动卸载操作
const unwatch = watchEffect(() => {})
unwatch()
------------------------------------------------------------------------------
```
### ref 元素/组件模版引用
![setup 同名变量获得元素模版的引用,ref与元素一一对应](image-18.png)<br/>
![setup 同名变量获得元素模版的引用,ref与元素一对多](image-19.png)<br/>
![动态ref,事件处理器获得元素模版的引用](image-20.png)<br/>
```
#通过组合式api的形式按需引入ref。
一 JS获得元素模版引用,元素唯一，ref与元素一一对应的情况.
<template>
	<input ref='input'>
</template>

<script setup>
import {ref,onMounted} from 'vue'
//模版同名变量获得模版元素的引用
const input = ref(null)
onMounted(()=>{//自动获得焦点。
	input.value.focus()
})

</script>

--------------------------------------------------------------------------------
二 js中获得元素模版引用,元素不唯一,多个元素对应同一个ref的情况,v-for 内部元素使用ref。
<template>
	<div v-for='(item,index) in list' ref='itemRefs' :key='index'>
		{{item}}
	</div>
</template>

<script setup>
import {ref,onMounted} from 'vue'
//模版同名变量获得模版元素的引用
const list = [1,2,3]
const itemRefs = ref([])
onMounted(()=>{//自动获得焦点。
	console.log(itemRefs.value)
})

</script>

-----------------------------------------------------------------
@事件处理器中获得元素模版的引用
<input :ref="(el)=>{/*将el赋值给ref变量*/}">

------------------------------------------------------------------
@组件模版应用
## Vue2
vue2中，ref指代的是组件实例本身，具有组件的完整访问控制权。但是推荐使用emit/on的形式进行父子组件的通讯问题。

## Vue3
vue3中,使用了setup的写法，只有指定暴露出来的组件属性可供父组件访问（公共属性）,默认情况下其它属性都是私有的。
选项式写法
------------------------------------------------------------------
export default {
  expose: ['publicData', 'publicMethod'],
  data() {
    return {
      publicData: 'foo',
      privateData: 'bar'
    }
  },
  methods: {
    publicMethod() {
      /* ... */
    },
    privateMethod() {
      /* ... */
    }
  }
}

组合式写法
---------------------------------------------------------------------
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// 像 defineExpose 这样的编译器宏不需要导入
defineExpose({
  b
})
</script>

此时通过ref获得组件引用时，只能访问属性b。
```
--------------------------------------------------------------------
### 父子组件通讯
@1 选项式写法:子组件内使用 props + emit
```
<!-- 父组件:test.vue -->
<template>
<div :style='{fontSize:postFontSize + "em"}'>
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
  @enlarge-text="enlargeText"
 />
</div>
</template>
<script>
 import BlogPost from './BlogPost.vue'
 export default() {
	data() {
		return {
			posts: [
					{ id: 1, title: 'My journey with Vue' },
					{ id: 2, title: 'Blogging with Vue' },
					{ id: 3, title: 'Why Vue is so fun' }
			],
			postFontSize: 1,
		}
	},
	methods:{
		enlargeText() {
			this.postFontSize +=0.1
		}
	},
 }


</script>

<!--子组件:BlogPost.vue -->
<script>
export default {
  props: ['title'],
  methods:{
	handleClick(){
		this.$emit('enlarge-text')
	}
  }
}
</script>

<template>
  <h4>{{ title }}</h4>
  <button @click='handleClick'>放大</button>
</template>




@2 组合式写法:子组件内使用 defineProps() + defineEmits()

<!-- 父组件:test.vue -->
<script setup>
import { ref } from 'vue'
import BlogPost from './BlogPost.vue'
  
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])

const postFontSize = ref(1)
</script>

<template>
	<div :style="{ fontSize: postFontSize + 'em' }">
    <BlogPost
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
      @enlarge-text="postFontSize += 0.1"
    ></BlogPost>
  </div>
</template>

<!-- 子组件:BlogPost.vue -->
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>

<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>

```

## Vue2系列
### vue实例生命周期示例图
![vue2 生命周期图示](image.png)<br/>
![new Vue()解读](image-3.png)<br/>
![beforemount前置行为](image-2.png)<br/>
```
@1.1 beforeCreate钩子能力和其前置行为
 前置行为: init Events & lifeCycle.
 钩子能力： 钩子不能访问data属性和this实例

@1.2 created钩子能力和其前置行为
 前置行为： init injections & reactive
 钩子能力：钩子能访问组件的data属性和this实例,这个阶段还不能操作dom

@1.3 beforeMount钩子能力和其前置行为
 前置行为：进行两个存在性的判断.（has el option / has template option）
 总结一下，"el"选项是将现有的HTML元素作为Vue实例的挂载目标，而"template"选项是使用一个HTML模板字符串作为Vue实例的挂载目标。

（el/template/el+template 三种形式的组合，template判断优先级高于el）
 先判断是否存在挂载元素“el”【可选判断】
 判断是否存在“template” 选项（template组件模板）【强制判断】
 有temlpate选项就将template compile template into render function(目前的单文件组件,主流项目是这个模式)
 无template选项则挂载el's outerHtml, compile  el's outerHtml as template.

在Vue2中，使用compile template into render function和compile el's outerHtml as template都可以将Vue模板编译成渲染函数，但它们的实现方式和使用场景略有不同。
 
 钩子能力：叠加created钩子的能力,也可以注册事件监听器和不依赖组件数据的第三方图表库的初始化。 以图表的形式进行总结@1.4 mounted钩子能力和其前置行为

 @1.4 mounted钩子能力和其前置行为
 ########
 这里的vm.$el(一般现在时),el（一般将来时）都是指代真实DOM。
 创建虚拟DOM发生在beforeMount钩子之后，mounted钩子之前。create vm.$el and replace el with it.
 创建虚拟dom,渲染虚拟dom,变成真实dom,用真实dom替换挂载点。
 ########
 mounted钩子在组件在挂载点挂载后立即执行。

@1.5 beforeUpdate钩子能力和其前置行为
 前置行为：data change 会触发beforeUpdate
 钩子能力： beforeUpdate只能在组件更新前执行一些同步任务

@1.6 updated钩子能力和其前置行为
 前置行为：virtual DOM re-render and patch(虚拟dom更新和打补丁)
 钩子能力： 可以访问到数据更新后的dom,并进行操作。

@1.7 beforeDestroy钩子能力和前置行为
 前置行为： vm.$destroy 被调用
 钩子能力： 清除定时器,事件监听，watchers，子组件等

@1.8 destroyed钩子能力和前置行为
 前置行为：
 钩子能力：

```
>小结: new Vue()的过程冲会依次触发不同阶段的钩子函数，以供开发者做相应的处理。核心记忆要点是
以单文件组件为模版还是外部HTML为模版创建Vue示例。<br/>

## vue3系列
```
1.1 性能提升
 1)打包大小体积减少，初次渲染、更新渲染更快，内存占用减少。

1.2 源码升级
 1)重写响应式的实现，使用proxy来替代原来的Object.defineProperty的实现。
 2)重写虚拟dom和Tree-Shaking的实现。

1.3 typescript支持,typescript的特性
 1)类型安全,提供了更好的代码可读性、可维护性和可靠性
 2)代码提示和自动补全
 3)易重构和维护
 4)vue3生态系统的支持
  
1.4 其它新的特性和组件
 1)组合式api(composition API)
   setup()/ref、reactive/watch、watchEffect/provid、inject.
 2)内置组件
   Fragment(片段)/Teleport(传送)/Suspense(悬念)
 3)其它改变
   生命周期钩子函数的重命名...
```
### 从零开始创建一个vue3的工程
```
2.1 通过vite创建vue3工程
 执行以下命令
 npm init vite-app projecName
 cd projecName
 npm i
 npm run dev

2.2 通过vue-cli脚手架创建vue3工程
Vue -V 版本>=4.5.0
 执行以下命令
 vue create projectName
 cd projectName
 npm run serve

```

![vue3 生命周期](image-1.png)

vue3 兼容vue2原来的配置项API,新增了组合式API。
### 配置项Api
```
<script>
export default(){
  name:'Demo',
  data() {
	return {
		msg: 'hello world'
	}
  },
  //通过配置项的形式使用生命周期钩子
   beforeCreate() {
		console.log('---beforeCreate---')
	},
	created() {
		console.log('---created---')
	},
	beforeMount() {
		console.log('---beforeMount---')
	},
	mounted() {
		console.log('---mounted---')
	},
	beforeUpdate(){
		console.log('---beforeUpdate---')
	},
	updated() {
		console.log('---updated---')
	},
	beforeUnmount() {
		console.log('---beforeUnmount---')
	},
	unmounted() {
		console.log('---unmounted---')
	}
}
</script>
```
### 组合式Api
需要特别留意的是，组合式api中没有创建态的两个生命周期钩子函数,组合式api的最大特定是按需引入api,
且所有钩子都在setup函数中按需定义和返回。
##### 组合式Api写法一
```
<script>
	// 导入使用的钩子函数
	import {ref,onBeforeMount,onMounted,onBeforeUpdate,onUpdated,onBeforeUnmount,onUnmounted} from 'vue'
	export default {
		name: 'Demo',
		setup(){
			let sum = ref(0)
			console.log('---setup---')
			//通过组合式API的形式去使用生命周期钩子
			onBeforeMount(()=>{
				console.log('---onBeforeMount---')
			})
			onMounted(()=>{
				console.log('---onMounted---')
			})
			onBeforeUpdate(()=>{
				console.log('---onBeforeUpdate---')
			})
			onUpdated(()=>{
				console.log('---onUpdated---')
			})
			onBeforeUnmount(()=>{
				console.log('---onBeforeUnmount---')
			})
			onUnmounted(()=>{
				console.log('---onUnmounted---')
			})
			return {sum}
		}
	}
</script>
```
##### 组合式API写法二
```
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>

```

### 新增概念
#### 响应式基础
![响应代理和原始值的不同](image-4.png)<br/>
```
实用技巧:无论是防抖和节流，只要实现是和定时器关联的,建议在created进行注册,unmounted（beforeDestroy）进行清除。
这样可以在组件复用的时候，避免彼此之间的互相影响。
```
无论是vue2还是vue3，它的响应式都是基于观察者模式，利用对象的数据劫持来实现的。依赖收集和依赖更新的过程，都在响应式的基础上进行。

一 ref()/reactive() 数据代理.
入参:ref无限制，reactive只能接受集合类型对象入参。
返回值:都会返回响应式对象。不同的是ref返回的是有额外多一层value属性的响应式对象，而reactive返回的是响应式对象本身。
注意：vue3 对同一个原始对象调用 reactive() /ref()总是会返回同样的代理对象。
```
结论:推荐使用ref函数来创建响应式数据。

![nextTick与DOM更新](image-5.png)






```
#### 响应式原理
```
vue2的响应式实现是基于Object.defineProperty的实现数据劫持，vue3的响应式实现是基于Proxy的实现数据劫持。
```
### vue3示例的生命周期
![实例的生命周期钩子](image-11.png)
![组合式和选项式的生命周期差异](image-12.png)
```
生命周期钩子的API都应该在组件的 setup() 阶段被同步调用.选项式下生命周期变更了卸载阶段的钩子名称;
组合式下没有了创建阶段的钩子（beforeCreate/created),使用了setup 阶段来平替,有相关逻辑写在此处即可。
```














## vue2 和 vue3的横向比较
关注vue3的变化
```
1 组件是否强制需要一个根元素。vue3不强制
2 main.js 是否强制需要引入vue构造。vue3不需要，而是引入createApp工厂模式后的方法
3 重命名组件生命周期的最后两个钩子名称

小结：vue3围绕创建应用的模式来理解。以xxx为模块创建应用，并挂载到指定容器。
const app = createApp(App) //以App组件为模块创建应用，得到应用实例
const rootComp = app.mount('#app') //将应用挂载到容器，得到根组件实例
```

