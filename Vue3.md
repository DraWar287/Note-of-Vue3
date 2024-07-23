# Vue3

[toc]
## 创建一个应用实例：
### 基本结构
```javascript
import { createApp } from 'vue'

const app = createApp({ 
  /* 根组件选项 */
})
/*  传给createApp的对象是一个组件 */

app.mount('#app') # 挂载应用
```

### 根组件模板
  * 通常是组件的一部分
  * 或者直接在挂载容器内

### 应用配置
  app.config允许配置错误处理等选项
********************
  
## 模板语法
### 文本插值：双大括号
### v-bind:响应式attibute绑定
```html
<div v-bind:id="dynamicId"></div>

<div :id="dynamicId"></div> /* 简写 */
```

* 绑定支持js表达式和函数调用
```javascript
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

* 指令： v- 前缀的特殊 attribute
  [内置指令](https://cn.vuejs.org/api/built-in-directives.html)
  * 指令期望值是js表达式，有的指令需要参数
  ```javascript
  <p v-if="seen">Now you see me</p>   /* 基于表达式 seen 的值的真假来移除/插入该 <p> 元素 */

  <a v-on:click="doSomething"> ... </a>
  <!-- 简写 -->
  <a @click="doSomething"> ... </a>
  ```
  * 指令参数可以为动态：用[arg]包起来,arg只能是字符串或null,null表示解除绑定
  ```javascript
  <a v-on:[eventName]="doSomething"> ... </a>
  ```
  * 注意：动态指令参数引号空格都不合法，以及避免使用大写字母

* 修饰符 Modifiers: 以点开头的特殊后缀，表明指令需要以一些特殊的方式被绑定


********************************

## 响应式
### ref: 接收参数，并将其包裹在一个带有 .value 属性的 ref 对象中返回
```js
import { ref } from 'vue'

const count = ref(0)
console.log(count) // { value: 0 }
console.log(count.value) // 0

```
* 从组件的setup()声明并返回ref以供访问
```js
import { createApp, ref } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

const MyComponent = {
  setup() {
    const count = ref(0)
    // expose the ref to the template
    return {
      count
    }
  }
}

createApp(MyComponent).mount('#app')
```
* 单文件组件```<script setup>```简化代码
```html
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

* 还可以用reactive() api来声明响应式状态，但主要的还是ref
* 注意：只有顶级的ref属性才能自动解包(如ref0.ref1不能自动解包)

*************************

## 计算属性
* compute() 期望接收一个getter函数，返回一个**计算属性**ref
  计算属性与一个方法的区别是：计算属性值会基于其响应式依赖被**缓存**

* 可写的计算属性：需要setter (大多数情况不使用)
```js
const firstName = ref('a')
const lastName = ref('b')
const fullName = computed({
  get() {
    return firstName.value + ' ' + lastName.value
  },
  set(newValue) {
    [firstName.value, lastName.value] = newValue.split(' ') /* 接受参数如"A B" */
  }
})
```

***************

## 类与样式绑定

### 绑定对象
* 给 ```:class``` 传递一个对象来动态切换 class
```js
/* active是否存在取决于isActive是否为true， isActive是一个ref属性 */
<div :class="{ active: isActive }"></div>
```
* 可直接绑定一个对象
```js
const classObject = reactive({
  active: true,
  'text-danger': false
})
```
  模板
```html
<div :class="classObject"></div>
```
* 还可以绑定一个计算属性
```js
const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
})) /* 这里用()内嵌{}表示返回一个对象 */
```

### 绑定数组
* 以给 ```:class``` 绑定一个数组来渲染多个 CSS class,
* 可以使用三元表达式
```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

### 在组件上使用
**[ Waiting for additional details ]**


### 绑定内联样式
#### 绑定对象，对应style属性
* ```:style``` 支持绑定 JavaScript 对象值, 对应style属性
```js
const activeColor = ref('red')
const fontSize = ref(30)
```
```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
* 常使用驼峰式命名， 也可以使用kebab-cased形式
```html
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```
* 直接绑定一个对象， 更简洁
```js
const styleObject = reactive({
  color: 'red',
  fontSize: '30px'
})
```
```html
<div :style="styleObject"></div>
```
#### 绑定多样式对象数组
* 绑定一个包含多个样式对象的数组。这些对象会被合并后渲染到同一元素上
```html
<div :style="[baseStyles, overridingStyles]"></div>
```
#### 自动前缀和样式多值
* Vue 会自动加上某些 CSS 属性需要的浏览器特殊前缀
* 对一个样式属性提供多个 (不同前缀的) 值， 数组仅会渲染浏览器支持的最后一个值
```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

******************

## 条件渲染