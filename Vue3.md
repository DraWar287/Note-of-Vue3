# Vue3
[toc]
## 创建一个应用实例：
* 基本结构
```javascript
import { createApp } from 'vue'

const app = createApp({ 
  /* 根组件选项 */
})
/*  传给createApp的对象是一个组件 */

app.mount('#app') # 挂载应用
```

* 根组件模板
  * 通常是组件的一部分
  * 或者直接在挂载容器内

* 应用配置
  app.config允许配置错误处理等选项

  
## 模板语法
* 文本插值：双大括号
* v-bind:响应式attibute绑定
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

## 



