[toc]
## 安装
```shell
$ npm install element-plus --save
```
## 导入
### 完整导入
```js
// main.ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'

const app = createApp(App)

app.use(ElementPlus)
app.mount('#app')
```

### 按需导入
1. 安装```unplugin-vue-components```和```unplugin-auto-import```这两款插件
```shell
npm install -D unplugin-vue-components unplugin-auto-import
```
2. 把下列代码插入到```Vite```的配置文件中
```js
// vite.config.ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```
3. 从官网选择组件复制代码
[Element Plus 组件库](https://element-plus.org/zh-CN/component/overview)

## 语言切换
* 在main.js中导入本地语言插件
```js
import locale from 'element-plus/dist/locale/zh-cn.js'
app.use(locale)
```

## 常用组件
* 表单
* 表格
* 分页
* 反馈组件