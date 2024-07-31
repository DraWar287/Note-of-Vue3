[toc]
# Vue3基础
## 创建一个应用实例：
### 基本结构
```js
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

### 深层监听器
* 直接给 watch() 传入一个响应式对象，会隐式地创建一个深层侦听器——该回调函数在所有嵌套的变更时都会被触发
* 一个返回响应式对象的 getter 函数，只有在返回不同的对象时，才会触发回调
* watch的第三个参数加上```{deep: true}```, 强制转为深层监听器
### 即时回调的监听器
* 创建侦听器时，立即执行一遍回调
* watch的第三个参数加上```{immediate: true}```
### 一次性侦听器
* watch的第三个参数加上```{once: true}```
### ```watchEffect```
  ```JavaScript
  watchEffect(async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  })
  ```
* 以上代码自动追踪 todoId.value 作为依赖（和计算属性类似）。每当 todoId.value 变化时，回调会再次执行。
* 回调会立即执行，不需要指定 immediate: true
### 回调触发时机
* 默认情况下，侦听器回调会在父组件更新 (如有) 之后、所属组件的 DOM 更新之前被调用
* 侦听器回调中能访问被 Vue 更新之后的所属组件的 DOM：指明```{flush: 'post'}```
*  ```watchEffect()``` 有个更方便的别名 ```watchPostEffect()```
* 同步监听器：```{flush: 'sync'}```, ```watchSyncEffect()```
### 停止侦听器
* 在 setup() 或 ```<script setup>``` 中用同步语句创建的侦听器，会自动绑定到宿主组件实例上，并且会在宿主组件卸载时自动停止。
* 手动停止：(异步回调创建的需要手动停止)
```JavaScript
const unwatch = watchEffect(() => {})
// ...当该侦听器不再需要时
unwatch()
```
**********
## 模板引用
* 在一个特定的 DOM 元素或子组件实例被挂载后，获得对它的直接引用
```html
<input ref="input">
```
### 访问模板引用
* 声明一个与模板的```ref```同名的```ref```
* 只可以在组件挂载后才能访问模板引用
* 当在```v-for```中使用模板引用时，对应的```ref```中包含的值是一个数组
```html
<script setup>
import { ref, onMounted } from 'vue'

// 声明一个 ref 来存放该元素的引用
// 必须和模板里的 ref 同名
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```  
### 函数模板引用
* 使用动态的```:ref```绑定才能够传入一个函数
* 每次组件更新时都被调用
* 元素引用作为函数第一个参数
* 当绑定的元素被卸载时，函数也会被调用一次，此时的```el```参数会是```null```
  ```html
  <input :ref="(el) => { /* 将 el 赋值给一个数据属性或 ref 变量 */ }">
  ```

### 组件上的ref
**[ Waiting for additional details ]**


************************

## 组件基础
### 定义一个组件
* 单文件组件： 定义在一个单独的```.vue```文件中
* 一个js对象: 模板是一个内联的字符串，或用id选择器选择一个元素

### 传递props
* ```props```：子组件接收父组件传递的一些参数
* 声明```props```:
```js
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```
* defineProps 会返回一个对象，其中包含了可以传递给组件的所有```props```

* 配合```v-for```使用：
  * 父组件导入了组件```BlogPost```  
  * 父组件有一个对象数组储存要用的props
  ```js
  const posts = ref([
    { id: 1, title: 'My journey with Vue' },
    { id: 2, title: 'Blogging with Vue' },
    { id: 3, title: 'Why Vue is so fun' }
  ])
  ```
  ```html
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
   ```
### 监听事件
* ```$emit```: 子组件上抛一个事件
```html
<!-- BlogPost.vue -->
<script setup>
defineEmits(['enlarge-text'])
</script>
```
```html
<!-- 向父组件抛出一个enlarge-text事件 -->
<button @click="$emit('enlarge-text')">Enlarge text</button>
```
* 父组件可对该事件做处理
```html
    <BlogPost
      @enlarge-text="postFontSize += 0.1"
    ></BlogPost>
```
* ```defineEmits```返回一个等同于```$emit```方法的 emit 函数

### 插槽
* 父组件向子组件中传递内容
* 通过占位符```</slot>```, 父组件传递进来的内容就会渲染在这里
  * 父组件中：
  ```html
  <AlertBox>
    Something bad happened.
  </AlertBox>
  ```
  * 子组件中
  ```html
  <!-- AlertBox.vue -->
  <template>
    <div class="alert-box">
      <strong>This is an Error for Demo Purposes</strong>
      <slot />
    </div>
  </template>
  ```
### 动态组件
* 通过```:is```属性来切换不同的组件
* 传递给```:is```的值：
  * 被注册的组件名
  * 导入的组件对象
```html
<!-- currentTab 改变时组件也改变 -->
<component :is="tabs[currentTab]"></component>
```

# 深入组件

## 组件注册
* 一个Vue组件在被使用前首先要被注册
### 全局注册
* 使用app的```component```方法
* 参数
  1. 组件名称
  2. 组件实现的对象(可以从.vue文件导入)
* component方法可以被链式使用
```js
app
  .component('ComponentA', ComponentA)
  .component('ComponentB', ComponentB)
  .component('ComponentC', ComponentC)
```

### 局部注册
* 在```<script setup>```导入的组件无需注册
* 否则需要在组件对象的components属性中显示注册
* 局部注册的组件在后代组件中不可用

## props

### props声明
* 字符串数组形式
```js
const props = defineProps(['foo'])
```
* 对象形式：key 是 prop 的名称，而值则是该 prop 预期类型的构造函数
```js
defineProps({
  title: String,
  likes: Number
})
```
* 使用一个对象绑定多个 prop
```html
<!-- post是一个对象 -->
<BlogPost v-bind="post" />
```

### props校验
```js
defineProps({
  // 基础类型检查
  // （给出 `null` 和 `undefined` 值则会跳过任何类型检查）
  propA: Number,
  // 多种可能的类型
  propB: [String, Number],
  // 必传，且为 String 类型
  propC: {
    type: String,
    required: true
  },
  // 必传但可为 null 的字符串
  propD: {
    type: [String, null],
    required: true
  },
  // Number 类型的默认值
  propE: {
    type: Number,
    default: 100
  },
  // 对象类型的默认值
  propF: {
    type: Object,
    // 对象或数组的默认值
    // 必须从一个工厂函数返回。
    // 该函数接收组件所接收到的原始 prop 作为参数。
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // 自定义类型校验函数
  // 在 3.4+ 中完整的 props 作为第二个参数传入
  propG: {
    validator(value, props) {
      // The value must match one of these strings
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // 函数类型的默认值
  propH: {
    type: Function,
    // 不像对象或数组的默认，这不是一个
    // 工厂函数。这会是一个用来作为默认值的函数
    default() {
      return 'Default function'
    }
  }
})
```
### Boolean类型转换
```
<!-- 等同于传入 :disabled="true" -->
<MyComponent disabled />

<!-- 等同于传入 :disabled="false" -->
<MyComponent />
```
* 一个 prop 被声明为允许多种类型时，若Boolean为第一个选项， Boolean 的转换规则也将被应用

## 事件
### 事件参数
* 给```$emit```提供额外的参数
```html
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```
```html
<MyButton @increase-by="(n) => count += n" />
```

### 事件校验
* 使用对象形式来描述，事件为key, value为一个函数，接受的参数就是抛出事件时传入```emit```的内容，返回一个布尔值来表明事件是否合法。

## 组件```v-model```

### 基本用法
* 使用```defineModel```
```html
<!-- Child.vue -->
<script setup>
const model = defineModel()

function update() {
  model.value++
}
</script>

<template>
  <div>Parent bound v-model is: {{ model }}</div>
  <button @click="update">Increment</button>
</template>
```
```html
<!-- Parent.vue -->
<Child v-model="countModel" />
```
父组件的```countModel```会与子组件的```input```元素绑定

### ```v-model```的参数
* 第一个参数为名称，第二个为额外的 prop 选项
```js
const title = defineModel('title', { required: true })
```
```html
<MyComponent v-model:title="bookTitle" />
```

### 处理```v-model```修饰符

#### 自定义修饰符
* 给```defineModel()```传入```get```和```set```这两个选项。这两个选项在从模型引用中读取或设置值时会接收到当前的值
```html
<!-- Child.vue -->
<script setup>
const [model, modifiers] = defineModel({
  set(value) {
    if (modifiers.capitalize) {
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
    return value
  }
})
</script>
```
```html
<!-- Parent.vue -->
<MyComponent v-model.capitalize="myText" />
```

## 插槽

```html
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>
```
```html
<button class="fancy-btn">
  <slot></slot> <!-- 插槽出口 -->
</button>
```

### 默认内容
* 为插槽指定默认内容
* 显式提供的内容会取代默认内容
```html
<button type="submit">
  <slot>
    Submit <!-- 默认内容 -->
  </slot>
</button>
```
### 具名插槽
* 一个组件中包含多个插槽出口
* ```<slot>```元素可以有一个特殊的 attribute:```name```，用来给各个插槽分配唯一的 ID，以确定每一处要渲染的内容
* 没有提供```name```的```<slot>```出口会隐式地命名为“default”。
* ```v-slot```有对应的简写```#```: ```<template v-slot:header> 可以简写为 <template #header>```

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```
```html
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```

### 条件插槽
* 根据插槽是否存在来渲染某些内容
```html
  <div v-if="$slots.header" class="card-header">
    <slot name="header" />
  </div>
```

### 动态插槽名
```html
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
```
### 作用域插槽
* 向一个插槽的出口上传递 attributes：：
```html
<!-- <MyComponent> 的模板 -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
```
* 接收到了一个插槽 props 对象：
```html
<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>
```

### 依赖注入
#### Provide:
* 注入名可以是字符串或Symbol
```html
<script setup>
import { provide } from 'vue'

provide(/* 注入名 */ 'message', /* 值 */ 'hello!')
</script>
```

* 应用层provide
```js
import { createApp } from 'vue'
const app = createApp({})
app.provide(/* 注入名 */ 'message', /* 值 */ 'hello!')
```

#### Inject
```html
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```
* 可向```inject```提供第二个参数为默认值
* 第三个参数表示默认值是否被当作一个工厂函数。
```js
const value = inject('key', () => new ExpensiveClass(), true)
```
* 使用```readonly()```包装，确保数据不能被注入方更改
```js
provide('read-only-count', readonly(count))
```

# 路由
## 安装
```shell
npm install vue-router@4
```
## 基本使用
* 创建路由实例：```createRouter()```
```js
/* 这段代码常放在 src/router/index.js 下
   然后在main.js中导入
*/

import { createWebHistory, createRouter } from 'vue-router'

import HomeView from './HomeView.vue'
import AboutView from './AboutView.vue'

/*  URL 路径映射到组件 */
const routes = [
  { path: '/', component: HomeView },
  { path: '/about', component: AboutView },
]

const router = createRouter({
  history: createWebHistory(),
  routes,
})

export default rooter
```
* 注册路由插件
```js
/* ....... */
/* router在 src/router/index.js 下才能这么导入 */
import router from '@/router'

const app = createApp(App)
app.use(router)
app.mount('#app')
```
* 该路由插件作用：
  * 全局注册 ```RouterView``` 和 ```RouterLink``` 组件。
  * 添加全局 ```$router``` 和 ```$route``` 属性。
  * 启用 ```useRouter()``` 和 ```useRoute()``` 组合式函数。
  * 触发路由器解析初始路由。

* 使用路由进行跳转
```js
import {useRooter} from 'vue-router'
const router = useRouter();
router.push('/'); /* 跳转到主界面 */
```

## 配置子路由
* 路由映射添加```children```属性
```js
const routes = [
  { path: '/', component: HomeView },
  { path: '/about', 
    component: AboutView
    children: [ /* children是一个route数组 */
      {path: '/user/password', component: UserResetPassWordVue},
      {path: '/user/avartar', component: UserAvartarVue},
      {path: '/article/manage', component: UserArticleManageVue}
    ]
  },
]
```
* 重定向
  * 使一个页面默认使用一个字路由
  * 设置一个属性```redirect```
```js
{ path: '/', 
  redirect: '/article/manage',
  component: HomeView },
```
# 网络

## 跨域请求
* 源策略要求网页只能与与其来源相同的资源进行交互，而不能与其他来源的资源进行交互。
* 需要在```vite.config.js```中配置代理

  * 前端调用的baseURL改成一个标识```'/api'```, 当前ajax请求的源会自动补齐到```'/api'```前面
  * 修改```vite.config.js```
  ```js
    /* 添加 */
    server: {
      proxy: {
        '/api': { /* 获取路径中包含 '/api' 的请求*/
          target: 'http://hostName:8080', /* 后台服务所在的源 */
          changeOrigin: true, /* 修改源 */
          rewrite: (path) => path.replace(/^\/api/, '') /*   '/api'替换为 ''   */
        }
      }
    }
  ```

# 状态管理```Pinia```
* ```Store```承载着全局状态
* 一个 Store 应该包含可以在整个应用中访问的数据
## 基本使用
### 安装
```shell
npm install pinia
```
### 使用```pinia```插件
```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const pinia = createPinia()
const app = createApp(App)

app.use(pinia)
app.mount('#app')
```

### 定义Store
* 命名规范： 以 `use` 开头且以 `Store` 结尾。
* 第一个参数是应用中```Store```的唯一 ID。
* 利用Setup Store语法定义store:
  * ```ref()``` 就是 ```state``` 属性
  * ```computed()``` 就是 ```getters```
  * ```function()``` 就是 ```actions```
```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++
  }

  return { count, doubleCount, increment }
})
```



## 状态持久化```Persist```
* 安装
```shell
npm install pinia-plugin-persist
```
* 将```persist```插件添加到```pinia```实例上
```js
import { createPinia } from 'pinia'
import piniaPersist from 'pinia-plugin-persist'

const pinia = createPinia()
pinia.use(piniaPersist)
```
* 基本使用：```defineStore```时， 第三个参数```persist```选项设置为```true```
```js
import { defineStore } from 'pinia'
import { ref } from 'vue'

export const useTokenStore = defineStore('token', () => {
    /* State */
    const token = ref('')

    /* Setter */
    const setToken = (newToken) => {
        token.value = newToken
    }

    /* Action */
    const removeToken = () => {
        token.value = ''
    }

    return {
        token, setToken, removeToken
    }
}, {
  persist: true
}
);
```