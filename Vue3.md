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
</script>

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
### v-if, v-else-if, v-else
```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```
* ```v-show```也能有类似效果，若需要频繁切换的话选择v-show较好
* 注意：```v-if``` 和```v-for```不推荐同时使用

******************

## 列表渲染
### v-for

```js
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```
```html
<li v-for="item in items">
  {{ item.message }}
</li>
```
* ```v-for``` 也支持使用可选的第二个参数表示当前项的位置索引。

### ```v-for```与对象
* 可以使用 v-for 来遍历一个对象的所有属性
```js
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```
```html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```
* 第二个可选参数为 key, 第三个可选参数为位置索引
```html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```
### ```v-for```里使用范围值
```html
<span v-for="n in 10">{{ n }}</span>
```
### 条件循环
* 避免在同一个标签内同时使用```v-if```和```v-for```
* 应当嵌套:
```html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```
### 通过```key```管理状态
* ```v-for```渲染的元素列表的默认更新是就地更新，即DOM元素的顺序不会再被移动
* 重用和重新排序现有的元素，要为每个元素对应的块提供一个唯一的```key```attribute
```html
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```
### 组件上使用```v-for```
**[ Waiting for additional details ]**

***********************


## 事件处理
### 监听事件
* ```v-on:```或其简写```@```
* 事件处理器：
  * [内联事件处理器](#内联事件处理器)
  * [方法事件处理器](#方法事件处理器)
### 内联事件处理器
* 事件被触发时执行的内联 JavaScript 语句
```js
const count = ref(0)
```
```html
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
```

### 方法事件处理器
* 一个指向组件上定义的方法的属性名或是路径
```html
<!-- `greet` 是上面定义过的方法名 -->
<button @click="greet">Greet</button>
```
### 内联事件处理器中访问事件参数
* 传入特殊参数 ```$event```
* 使用内联箭头函数
```html
<!-- 使用特殊的 $event 变量 -->
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

<!-- 使用内联箭头函数 -->
<button @click="(event) => warn('Form cannot be submitted yet.', event)">
  Submit
</button>
```
```js
function warn(message, event) {
  // 这里可以访问原生事件
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

### 事件修饰符
```html
<!-- 单击事件将停止传递 -->
<a @click.stop="doThis"></a>

<!-- 提交事件将不再重新加载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰语可以使用链式书写 -->
<a @click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form @submit.prevent></form>

<!-- 仅当 event.target 是元素本身时才会触发事件处理器 -->
<!-- 例如：事件处理器不来自子元素 -->
<div @click.self="doThat">...</div>

<!-- 添加事件监听器时，使用 `capture` 捕获模式 -->
<!-- 例如：指向内部元素的事件，在被内部元素处理前，先被外部处理 -->
<div @click.capture="doThis">...</div>

<!-- 点击事件最多被触发一次 -->
<a @click.once="doThis"></a>

<!-- 滚动事件的默认行为 (scrolling) 将立即发生而非等待 `onScroll` 完成 -->
<!-- 以防其中包含 `event.preventDefault()` -->
<div @scroll.passive="onScroll">...</div>
```

### 按键修饰符
```html
<!-- 仅在 `key` 为 `Enter` 时调用 `submit` -->
<input @keyup.enter="submit" />
```
* 按键别名：
  * ```.enter```
  * ```.tab```
  * ```.delete``` (捕获“Delete”和“Backspace”两个按键)
  * ```.esc```
  * ```.space```
  * ```.up```
  * ```.down```
  * ```.left```
  * ```.right```
* 系统按键别名：
  * ```.ctrl```
  * ```.alt```
  * ```.shift```
  * ```.meta``` (win键, cmd键)
* ```.exact```修饰符
  精确控制触发事件所需的系统修饰符的组合
  ```html
  <!-- 当按下 Ctrl 时，即使同时按下 Alt 或 Shift 也会触发 -->
  <button @click.ctrl="onClick">A</button>
  <!-- 仅当按下 Ctrl 且未按任何其他键时才会触发 -->
  <button @click.ctrl.exact="onCtrlClick">A</button>
  <!-- 仅当没有按下任何系统按键时触发 -->
  <button @click.exact="onClick">A</button>
  ```
### 鼠标按键修饰符
* ```.left```
* ```.right```
* ```.middle```

**********

## 表单输入绑定
* ```v-model``` 指令绑定```value```(或```checked```)proprety
* ```v-model``` 指令监听```input```(或```change```)事件
### 基本用法
#### 文本
```html
<p>Message is: {{ message }}</p>
<input v-model="message" placeholder="edit me" />
```
#### 多行文本
```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```
#### 复选框
* 单一复选框，绑定布尔类型
```html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```
* 多个复选框绑定到同一个数组
```js
const checkedNames = ref([])
```
```html
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```
#### 单选
```html
<div>Picked: {{ picked }}</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">One</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Two</label>
```
#### 选择器
* 单选
```html
<div>Selected: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```
* 多选(值绑定到一个数组)
```html
<div>Selected: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```
* ```v-for```动态渲染
```js
const selected = ref('A')

const options = ref([
  { text: 'One', value: 'A' },
  { text: 'Two', value: 'B' },
  { text: 'Three', value: 'C' }
])
```
```html
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>

<div>Selected: {{ selected }}</div>
```

### 修饰符
* ```.lazy```： 每次 change 事件后才更新数据
* ```.number```： 输入自动转化为数字
  ```html
  <input v-model.number="age" />
  ```
* ```.trim```： 去除输入的两端空格

*********************

## 生命周期
### 生命周期
  * 主要包含创建、挂载、更新和销毁组件阶段
  * 生命周期钩子：在生命周期不同阶段执行特定代码
### 生命周期钩子
* 注册生命周期钩子：
```html
<script setup>
import { onMounted } from 'vue'
/* 组件完成初始渲染并创建 DOM 节点后运行代码 */
onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```
* [生命周期钩子的API索引](https://cn.vuejs.org/api/composition-api-lifecycle.html)

******************************

## 侦听器

### ```watch```函数
*  watch 函数在每次响应式状态发生变化时触发回调函数
```js
// 可以直接侦听一个 ref
const question = ref('')
watch(question,  (newQuestion, oldQuestion) => {
  // 具体实现
})
```
* ```watch```的第一个参数可以是：
  * 一个ref
  * 一个响应式对象
  * 一个getter函数
  * 多个数据源组成的数组


