---
title: Vuex入门及使用
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-02-22 15:54:46
password:
summary:
tags:
- 前端
- Vue
- Vuex
categories:
- [前端, Vue ]
id: 1
---
一个小例子掌握Vuex入门知识
<!--More-->

自己也是通过学习视频才了解的，前排感谢 B站up主 [ 云繁EST ](https://www.bilibili.com/video/BV1h7411N7bg?p=1)
# Vuex概述
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。Vuex 是实现组件全局状态（数据）管理的一种机制，可以方便地实现组件之间数据的共享。
# Vuex的基本使用
## 1.安装vuex依赖包
```js 安装vuex依赖包
npm install vuex --save
```
## 2.导入Vuex包
```js 导入
import Vuex from 'vuex'
// 使用
Vue.use(Vuex)
```
## 3.创建store对象
```js 创建store对象
const store = new Vuex.store({
  // state 中存放的就是全局共享的数据
  state: {
    count: 0
  }
})
```
## 4.将store对象挂载到 vue 实例中
```js 将store对象挂载到 vue 实例中
new Vue({
  router,
  // 将创建的共享数据对象，挂载到Vue实例中
  // 所有组件中，都可以直接从 store 中获取全局的数据
  store,
  render: h => h(App)
}).$mount('#app')
```
我们可以通过一个小例子来熟悉vuex的用法：[父组件 App.vue 上引入两个子组件，一个加法操作的 add.vue 子组件，一个减法操作的 minus.vue 子组件。父组件引入并渲染 Vuex 中的 全局共享的数据 count，通过加减法的运算来了解 count发生的变化。]{.blue}
* 我们把两个组件分隔开 准备工作完成后的 App.vue 代码如下：
```js 代码
<template>
  <div>
    ...
    <Add></Add>
    <p>--------------------------</p>
    <Minus></Minus>
  </div>
</template>
<script>
import Add from './components/add'
import Minus from './components/minus'
export default {
  data () {
    return { }
  },
  components: {
    Add,
    Minus
  }
}
</script>

```
# State
State提供唯一的公共数据源，所有共享的数据都要统一放到Store的State中进行存储。
* 我们需要在add 和 minus 组件中 都能访问到 当前最新的 count 值 有如下两种方式
## 方法一：this.$store.state.全局数据名称
* 在add组件中的代码
```js codes
    <h3>当前count值为：{{$store.state.count}}</h3>
```
## 方法二：①从vuex中按需引入 mapState 对象 ②将全局数据映射为当前组件的计算属性
* 在minus组件中的代码
```js codes
// 从vuex中按需引入 mapState 对象
import { mapState } from 'vuex'
// 将全局数据映射为当前组件的计算属性
computed: {
  ...mapState(['count'])
}
// 页面代码
<h3>当前count值为：{{count}}</h3>
```
# Getter
Getter 用于对Store 中的数据进行加工处理形成新的数据
① Getter可以对Store中已经存在的数据加工处理形成新的数据，类似Vue的计算属性
② Store 中数据发生变化，Getter的数据也会跟着变化
* 我们可以在store的state中新加一个 doubleCount 返回当前 count 数值的 2倍
```js codes
export default new Vuex.Store({
  state: {
    count: 0
  },
  getters: {
    doubleCount (state) {
      return `看我翻倍----  ${state.count * 2}`
    }
  }
  ...
})
```
* Getter 相当于计算属性，使用方法与 state中的数据一样
```js codes
// 在add组件中的代码
<h3>当前doubleCount值为：{{$store.state.doubleCount}}</h3>
// 在minus组件中的代码
// 从vuex中按需引入 mapGetters 对象
import { mapGetters } from 'vuex'
// 将全局数据映射为当前组件的计算属性
computed: {
  ...mapGetters(['doubleCount'])
}
```
# Mutation
Mutation 用于变更 Store 中的数据
① 只能通过 Mutation 变更 Store 中的数据，不可以直接操作 Store 中的数据
② 通过这种方式虽然操作稍微繁琐，但是可以集中监控所有公共数据的变化
```js codes
// store 目录下的 index.js 定义 Mutation
export default new Vuex.Store({
  ...
  mutations: {
    // 接收的参数 一个是 store里面的state，另一个是我们传的其他数据
    add (state, step) {
      // 如果无参数
      step ? state.count += step : state.count++
    },
    minus (state, step) {
      step ? state.count -= step : state.count--
    }
  },
})
```
* 我们可以在add组件和minus组件中添加按钮，动态改变 State 中 count 的值，有以下两种方式
## 方法一: this.$store.commit('方法名称', 其他参数)
```js codes
// add组件代码
<button @click="add1"> +1 </button>
<button @click="add2"> +N </button>
...
methods: {
  add1 () {
    this.$store.commit('add')
  },
  add2 () {
    this.$store.commit('add', this.numN)
  }
}
```

## 方法二：①从vuex中按需引入 mapMutations 函数 ②将需要的 mutations 函数，映射为当前组件的 methods 方法
```js codes
// minus组件代码
<button @click="reduce1"> -1 </button>
<button @click="reduce2"> -N </button>
import { mapMutations } from 'vuex'
···
methods: {
  ...mapMutations(['minus']),
  reduce1 () {
    this.minus()
  },
  reduce2 () {
    this.minus(2)
  }
}
```
# Action
Action 用于处理异步任务。
如果通过异步操作变更数据，必须通过 Action，而不能使用 Mutation，但是在 Action中还是要通过触发 Mutation的方式间接变更数据。
* 我们可以在store目录下的 index.js中声明两个 异步方法 addAsync 和 minusAsync
```js codes
export default new Vuex.Store({
  ···
  mutations: {
    add (state, step) {
      step ? state.count += step : state.count++
    },
    minus (state, step) {
      step ? state.count -= step : state.count--
    }
  },
  actions: {
    addAsync (context, step) {
      setTimeout(() => {
        context.commit('add', step)
      }, 500)
    },
    minusAsync (context, step) {
      setTimeout(() => {
        context.commit('minus', step)
      }, 500)
    }
  }
})
```
* 触发 Action 函数的两种方式
## 方法一：this.$store.dispatch('方法名称', 其他参数)
```js codes
// add组件代码
// 页面
<button @click="add3"> +N Async </button>
···
methods: {
  add3 () {
    this.$store.dispatch('addAsync', 3)
  }
}
```
## 方法二：①从 vuex 中按需引入 mapActons 函数 ②将指定的 actions 函数，映射为当前组件的 methods 函数
```js codes
// minus组件代码
// 页面代码
<button @click="reduce3"> -{{numN}} Async </button>
···
import { mapActions } from 'vuex'
···
methods: {
  ...mapActions(['minusAsync'])
  reduce3 () {
    this.minusAsync(2)
  }
}
```
# Module
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：
```js codes
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```
* 对于模块内部的 mutation 和 getter，接收的第一个参数是模块的局部状态对象。
```js codes
const moduleA = {
  state: () => ({
    count: 0
  }),
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```
* 同样，对于模块内部的 action，局部状态通过 context.state 暴露出来，根节点状态则为 context.rootState
```js codes
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      // tate(局部状态),commit,roosState（根状态）
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```
* 对于模块内部的 getter，根节点状态会作为第三个参数暴露出来：
```js codes
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      // state(局部状态),getters（全局getters对象）,roosState（根状态）
      return state.count + rootState.count
    }
  }
}
```
## Module 其他内容
* Module 其他内容 请移步官网 [ modules ](https://vuex.vuejs.org/zh/guide/modules.html)
--- 
[:heart:筱何仔]{.label .danger}