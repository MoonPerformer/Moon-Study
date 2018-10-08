## quill-editor

- 安装：`npm i -S vue-quill-editor`
- [vue-quill-editor](https://github.com/surmon-china/vue-quill-editor)
- 导入

```js
import Vue from 'vue'
import VueQuillEditor from 'vue-quill-editor'

// require styles
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

Vue.use(VueQuillEditor, /* { default global options } */)
```

- 使用

```html
<quill-editor
  v-model="goodsFormAdd.goods_introduce"
  class="goods-editor">
</quill-editor>
```

## 按需加载

- 1 修改 `router/index.js` 中导入组件的语法

```js
// 使用：
const Home = () => import('@/components/home/Home')
// 替换：
// import Home from '@/components/home/Home'

// 给打包生产的JS文件起名字
const Home = () => import(/* webpackChunkName: 'home' */ '@/components/home/Home')

// chunkName相同，将 goods 和 goods-add 两个组件，打包到一起
const Goods = () => import(/* webpackChunkName: 'goods' */'@/components/goods')
const GoodsAdd = () => import(/* webpackChunkName: 'goods' */'@/components/goods-add')
```

- 2 修改 `/build/webpack.prod.conf.js` 中的chunkFilename

```js
{
  // [name] 代替 [id]
  chunkFilename: utils.assetsPath('js/[name].[chunkhash].js')
}
```

## 使用CDN

- [开源项目 CDN 加速服务](https://www.bootcdn.cn/)
- 1 在 `index.html` 中引入CDN提供的JS文件
- 2 在 `/build/webpack.base.conf.js` 中（resolve前面）添加配置 externals
- **注意**：通过CDN引入 element-ui 的样式文件后，就不需要在 main.js 中导入 element-ui 的CSS文件了。所以，直接注释掉 main.js 中的导入 element-ui 样式即可
- `externals`配置：

```js
externals: {
  // 键：表示 导入包语法 from 后面跟着的名称
  // 值：表示 script 引入JS文件时，在全局环境中的变量名称
  vue: 'Vue',
  axios: 'axios',
  'vue-router': 'VueRouter',
  'element-ui': 'ELEMENT',
}

import ElementUI from 'element-ui'
```

### 常用包CDN

- [vue](https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.min.js)
- [vue-router](https://unpkg.com/vue-router@3.0.1/dist/vue-router.min.js)
- [axios](https://unpkg.com/axios/dist/axios.min.js)
- [element-ui JS](https://unpkg.com/element-ui/lib/index.js)
- [element-ui CSS](https://unpkg.com/element-ui/lib/theme-chalk/index.css)
- 说明：
  - 1 先在官方文档查找提供的CDN
  - 2 如果没有，在 `https://www.bootcdn.cn/` 或其他 CDN提供商 查找

## 缓存和保留组件状态

- [keep-alive](https://www.jianshu.com/p/0b0222954483)
- 解决方式：使用 `keep-alive` ，步骤如下：

```html
1 在需要被缓存组件的路由中添加 meta 属性
  meta 属性用来给路由添加一些元信息（其实，就是一些附加信息）
{
  path: '/',
  name: 'home',
  component: Home,
  // 需要被缓存
  meta: {
    keepAlive: true
  }
}

2 修改路由出口，替换为以下形式：
  根据 meta 是否有 keepAlive 属性，决定该路由是否被缓存
<keep-alive>
  <!-- 这里是会被缓存的视图组件 -->
  <router-view v-if="$route.meta.keepAlive">
  </router-view>
</keep-alive>

<!-- 这里是不被缓存的视图组件 -->
<router-view v-if="!$route.meta.keepAlive">
</router-view>
```

## 启用路由的 History 模式

- 通过在路由中添加 `mode: 'history'` 可以去掉 浏览器地址栏中的 #
- 在开发期间，只需要添加这个配置即可
- 但是，在项目打包以后，就会出现问题

```html
// 去掉 # 后，地址变为：

http://localhost:8080/#/goods
表示访问http://localhost:8080/index.html

http://localhost:8080/goods
表示访问了http://localhost:8080/goods


那么，服务器需要正确处理 /goods 才能正确的响应内容，
但是，/goods 不是服务端的接口，而是 用来在浏览器中实现 VueRouter 路由功能的
```

### 后端的处理方式

- 1 优先处理静态资源
- 2 对于非静态资源的请求，全部统一处理返回 index.html
- 3 当浏览器打开 index.html 就会加载 路由的js 文件，那么路由就会解析 URL 中的 /login 这种去掉#的路径了