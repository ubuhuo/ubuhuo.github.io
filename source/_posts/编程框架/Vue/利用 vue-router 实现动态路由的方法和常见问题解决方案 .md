---
title: Vue Router 实现动态路由和常见问题解决方案
date: 2020/03/05
categories:
  - 编程框架
  - Vue
tags:
  - Vue Router
  - 动态路由
copyright: true
---

## 何为动态路由

个人理解：动态路由不同于常见的静态路由，可以根据不同的「因素」而改变站点路由列表。常见的动态路由大都是用来实现：多用户权限系统不同用户展示不同导航菜单。

## 如何利用Vue Router 实现动态路由

`Vue` 项目实现动态路由的方式大体可分为两种：
1. 前端将全部路由规定好，登录时根据用户角色权限来动态展示路由；
2. 路由存储在数据库中，前端通过接口获取当前用户对应路由列表并进行渲染；

第一种方式在很多 `Vue UI Admin` 上都实现了，可以去读一下他们的源码理解具体的实现思路，这里就不过多展开。
第二种方式现在来说也比较常见了，因为近期项目正好用到所以单独讲一下，这里我使用的方案是利用 `Vue Router` 的一些特性实现后端主导的动态路由。

### 使用到的功能特性

#### Vue Router 全局前置守卫

[官网解释][2]

这里我们主要借助全局前置守卫的「前置」特性，在页面加载前将当前用户所用到的路由列表注入到 `Router` 实例中，注入使用到的方法则是下面的 `router.addRoutes` 方法。

#### Vue Router router.addRoutes 实例方法

[官网解释][4]

`router.addRoutes` 方法可以为 `Router` 实例动态添加路由规则，刚好为我们实现动态路由提供了注入方法。

#### Vue Router 路由懒加载

[官网解释][5]

懒加载这个功能不是动态路由的必要功能，但既然提供了这一特性，所以就直接在项目中使用了。

### 具体思路

#### 基础信息准备

前端代码实现基本静态路由，例如：登录页路由，服务器错误页路由等（这里有一个坑，后面讲）。数据库存储全部动态路由信息。

数据库如何存储动态路由信息？
我选择的方案是现将路由引用的对象字符串化，再将路由列表转化为 `JSON` 格式传输给后端，经后端处理后存储到数据库里。总之在前后端进行传递的是 `JSON` 格式的路由列表信息。

如何将路由中引用的对象字符串化？
我遇到的实际问题是：使用的 `UI` 组件提供了布局方案，需要引用布局组件并在子路由处引用具体页面。
我选择的解决方案是：区别对待需要引用布局组件的 `component` 属性，使用简短字符串代替布局组件，使用文件路径字符串代替页面引入。
具体实现可以看后面的代码实例。

#### 利用全局前置守卫对路由信息进行判断

1-判断用户是否登录
1.1-若未登录，跳转至登录页面
1.2-若已经登录，判断是否已获取路由列表
1.2.1-若未获取，从后端获取、解析并保存到 `Vuex` 中
1.2.2-若已获取，跳转至目标页面

这里我没做太多考察，直接将取到数据存储到了 `Vuex` 中，在实际项目应用的过程中应考虑数据存储的安全性。

如何实现路由列表解析？
1. 将 `JSON` 格式的路由信息解析为 `JavaScript` 列表对象；
2. 利用列表对象的 `filter` 方法实现解析函数，通过 `component` 判断是否为布局组件；
3. 若为布局组件，使用布局组件代替 `component` 字符串；
4. 若为具体页面，使用 `loadView` 函数加载对应的具体页面；

#### 利用 router.addRoutes 方法动态添加路由

这一步就很简单了，将解析好的路由列表通过 `router.addRoutes` 方法添加到 `Router` 实例中即可。

### 简单的实现代码

```javascript
// router/index.js
import Vue from 'vue'
import store from '@/store'
import Router from 'vue-router'
import { getToken } from '@/lib/util'

Vue.use(Router)

// 定义静态路由
const staticRoutes = [
  {
    path: '/login',
    name: 'login',
    meta: {
      title: '登录页面',
      hideInMenu: true
    },
    component: () => import('@/view/login/login.vue')
  },
  {
    path: '/401',
    name: 'error_401',
    meta: {
      hideInMenu: true
    },
    component: () => import('@/view/error-page/401.vue')
  },
  {
    path: '/500',
    name: 'error_500',
    meta: {
      hideInMenu: true
    },
    component: () => import('@/view/error-page/500.vue')
  }
]

// 定义登录页面名称（为了方便理解才定义的）
const LOGIN_PAGE_NAME = 'login'

// 实例化 Router 对象
const router = new Router({
  staticRoutes,
  mode: 'history'
})

// 定义全局前置守卫（里面有两个坑要注意）
router.beforeEach((to, from, next) => {
  // 通过自定义方法获取用户 token 用来判断用户登录状态
  const token = getToken()
  if (!token && to.name !== LOGIN_PAGE_NAME) {
    // 如果没有登录而且前往的页面不是登录页面，跳转到登录页
    next({ name: LOGIN_PAGE_NAME })
  } else if (!token && to.name === LOGIN_PAGE_NAME) {
    // 如果没有登录而且前往的页面是登录页面，跳转到登录页面
    // 这里有一个坑，一定要注意这一步和上一步得分开写
    // 如果把前两步判断合并为 if (!token) next({ name:login })
    // 则会形成登录页面无限刷新的错误，具体成因后面解释
    next()
  } else {
    // 如果登录了
    if (!store.state.app.hasGetRoute) {
      // 如果没有获取路由信息，先获取路由信息而后跳转
      store.dispatch('getRouteList').then(() => {
        router.addRoutes(store.state.app.routeList)
        // 这里也是一个坑，不能使用简单的 next()
        // 如果直接使用 next() 刷新后会一直白屏
        next({ ...to, replace: true })
      })
    } else {
      // 如果已经获取路由信息，直接跳转
      next()
    }
  }
})

export default router
```

```javascript
// store/index.js
import router from '@/router'
import Main from '@/components/main'
import { getToken } from '@/lib/util'
import { getRoute } from '@/api/app'

const loadView = (viewPath) => {
  // 用字符串模板实现动态 import 从而实现路由懒加载
  return () => import(`@/view/${viewPath}`)
}

const filterAsyncRouter = (routeList) => {
  return routeList.map((route) => {
    if (route.component) {
      if (route.component === 'Main') {
        // 如果 component = Main 说明是布局组件
        // 将真正的布局组件赋值给它
        route.component = Main
      } else {
        // 如果不是布局组件就只能是页面的引用了
        // 利用懒加载函数将实际页面赋值给它
        route.component = loadView(route.component)
      }
      // 判断是否存在子路由，并递归调用自己
      if (route.children && route.children.length) {
        route.children = filterAsyncRouter(route.children)
      }
    }
  })
}

export default {
  state: {
    routeList: [],
    token: getToken(),
    hasGetRoute: false
  },
  mutations: {
    setRouteList(state, data) {
      // 先将 JSON 格式的路由列表解析为 JavaScript List
      // 再用路由解析函数解析 List 为真正的路由列表
      state.routeList = filterAsyncRouter(JSON.parse(data))
      // 修改路由获取状态
      state.hasGetRoute = true
    }
  },
  atcions: {
    getRouteList({ state, commit }) {
      return new Promise((resolve) => {
        const token = state.token
        getRoute({ token }).then((res) => {
          let data = res.data.data
          // 注意这里取出的是 JSON 格式的路由列表
          commit('setRouteList', data)
          resolve()
        })
      })
    }
  }
}
```


## 常见问题

### 页面卡在登录页面而且不断刷新

这个问题的解决方案在「实现代码」中已经提到了，只需要在判断登录状态的时候注意不要将两种未登录状态混为一谈即可。但这样治标不治本，因为同样的问题可以由不同形式的代码导致，那导致问题的原因是什么那？然我们慢慢分析：

我们先假设不小心把两种未登录的状态混在一起判断：

```javascript
if (!token) {
  next({ name: LOGIN_PAGE_NAME })
}
```

这里的 `next({ name: LOGIN_PAGE_NAME })` 方法会再一次激活全局前置守卫，从而导致再一次进入判断并触发 `next({ name: LOGIN_PAGE_NAME })`，如此递归调用下去，页面就会卡主并且不断刷新。

### 动态路由配合路由懒加载

实现这一目的的方案也在代码示例中展示了：

```javascript
const loadView = (viewPath) => {
  return () => import(`@/view/${viewPath}`)
}
```

这里是运用了一个 JavaScript 不太常用的特性：字符串模板，使用此特性让不支持字符串拼接的 `import` 操作能够实现动态 `import` 不同的模块。

### 动态路由刷新后 404

这应该是本方案中最常见的一个错误之一，其原意是很多人在创建「基本静态路由」的时候回把 404 页面的路由也加入在里面，从而导致页面加载初期动态路由还没有加入到路由实例中，匹配范围最广的 404 页面就会跳出来。解决方法就是将 404 页面的路由也加入到动态路由中。

### 动态路由刷新后变空白页

造成这一问题的原因有很多，我这里遇到的问题是使用 [参考文章3][6] 解决的，但具体原理我还没弄清楚，等我做一下研究再来更新。

### 动态路由页面刷新时 Title 不稳定

造成这一问题的原因很简单：因为页面刷新的时候路由信息还没加载进来，所以根本没有标题信息可供加载。但是我还没找到比较好的解决方案，同样等我研究一下再更新。

## 参考

1. [大师兄：Vue 动态路由的实现……][1]
2. [Vue Router 文档页面][3]
3. [rambo：vue router 动态路由 刷新后变空白页][6]

[1]: https://segmentfault.com/a/1190000015419713
[2]: https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E5%85%A8%E5%B1%80%E5%89%8D%E7%BD%AE%E5%AE%88%E5%8D%AB
[3]: https://router.vuejs.org/zh/
[4]: https://router.vuejs.org/zh/api/#router-addroutes
[5]: https://router.vuejs.org/zh/guide/advanced/lazy-loading.html#%E6%8A%8A%E7%BB%84%E4%BB%B6%E6%8C%89%E7%BB%84%E5%88%86%E5%9D%97
[6]: https://segmentfault.com/q/1010000015875314