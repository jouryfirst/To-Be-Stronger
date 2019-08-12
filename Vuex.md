# 新手向Vuex学习之路

> 　　作为一个前端菜鸡，完全的非科班菜鸡初学新手，在学习Vue的道路上坎坎坷坷，老大甩过来vuex官方文档让我看，一开始看的晦涩难懂，半天理解不了里面的表述。因此，我在此记录下对vuex的入门总结，针对完全的新手有个相对白话一些的表述。

### 1. 简介

> 官方中文文档中对Vuex的解释是：Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用**集中式存储管理**应用的所有组件的状态，并以相应的**规则**保证状态以一种**可预测的**方式发生**变化**。

　　上面一段描述在初学者看来比较难明白，什么叫状态管理模式？集中式存储又是怎样的？在官方的英文文档中，状态管理模式是由“**\<i\>state management pattern +library\</i\>**”这样一段词汇翻译过来的，这样看来就更容易理解一些。state是一个核心词汇，简单来说，vue应用中所有的数据(data)、属性(computed)都可以是state。另外，“+library”这个词汇就很灵性了，解释了什么叫集中式存储，就类似一个图书馆一样，把应用中用到的state集中放到一个地方，当调用时就向借书一样从这个集中放置的图书馆借阅。\</br\>
　　这么描述起来，仿佛Vuex很复杂，我在Vue里直接可以从data里访问的数据，还要经历建造library、存放state、借阅state等过程，多麻烦。官方文档中对此也有说明，Vuex对于小型应用中，使用Vuex是繁琐冗余的，但是在中大型应用中，就需要管理共享state，Vuex的方便之处就体现出来了。就如同之前写过的组件通信，假如有很多组件，共同需要获取一个数据，那个数据存放文件不可能暴露在这些组件的Bus中，所以就需要将这个数据或者说状态放在一个library (\<i\>**在Vuex中这个核心叫做Store【仓库】，后面将使用store这个称呼**\</i\>)中，每个组件调用时，直接从store中响应式读取即可。\</br\>
　　简单来说，Vuex是一种基于Flux的前端状态管理框架，就是用来集中管理数据，就像是一个管家，将vue中需要的state存放在一个store中，当使用者想使用或者改变state时，不能自己进到store里随便拿，需要让管家先从store中提交(commit)一个变化，类似于记录一个调档账本，在中大型项目中可以方便地获取state的变化情况。
　　
### 2.基本用法

#### 2.1 安装vuex

npm install vuex -save

#### 2.2 创建store.js文件，在main.js中导入并配置store

#### 2.3 编辑store.js文件

　　Vuex的核心就是Store，前面说过store相当于是一个图书馆，一个store实例中包含以下属性的方法：
- state       定义state(状态、数据）
- getters     用来获取属性
- actions     定义方法（行为）
- commit      提交变化，修改state的唯一方式就是显式地提交mutation
- mutations   定义变化
  注：不能直接修改数据，必须显式提交变化，目的是为了追踪到状态的变化

#### 2.4 编辑App.vue

在子组件中访问store对象的两种方式：
方式1：通过this.$store访问
方式2：通过mapState、mapGetters、mapMutaions、mapActions访问（下文会具体对应细述）

### 3. 核心概念

#### 3.1 导图

![Vuex导图]()(https://user-gold-cdn.xitu.io/2019/7/23/16c1f442bb99b6ac?w=701&h=551&f=png&s=8112)

#### 3.2 State

　　State是vuex的核心概念中的核心，vuex就是state的管理工具，主要的就是state。state翻译过来就是状态，也可以是数据。在这里的State指的是在store实例中定义的state属性，就类似vue中的data，用来存放数据，可以是字符串，也可以是对象、数组等等。
```
`var state ={
   count:6
}
```
`上面一段代码是放在store.js中的一段代码，作用就是存放一个数据count，它的值为6。

　　在App.vue中该怎么调取store中的数据呢？Vuex提供了mapState的方法用来获取state，
在App.vue中，需要使用import {mapState} from 'vuex'将mapState方法导入进去，然后把mapState作为计算属性传进去：
```
`computed:mapState([
]()'count'   //这里的count指向的就是store.js中的state.count
])

```
`
#### 3.3 Getter

　　Getter作为store的属性来说，更像是计算属性的功能，用来派生出State里经过某些过滤、计算等方法后的状态。getters里也可以存放数据，但是与state不同的是，它里面存放的数据要以函数返回值的方式return出来。就例如：
```
`var getters = {
count1(state){
return state.count;
}
}
```
`在getters里面可以定义一个名为count1的函数，这个函数接受state作为其第一个参数，这里的state就是store的state属性，在函数里返回state中key为count的value，也就是6。

　　getter最常见的作用是用来过滤state中的数据，这一点就很像是计算属性了:
```
`var getters = {
count1(state){
return state.count;
}，
isEvenOrOdd(state){
return state.count%2==0?'偶数':'奇数';
}
}
```
`
　　Vuex提供了mapGetters的方法用来获取getters，与获取state的方法类似，也需要先import进去，然后再映射在computed上：
　　
```
`import {mapState,mapGetters,mapActions} from 'vuex'

computed:mapGetters([
]()  'count1', //用来获取数据，这里count1是函数名，真正的数据是这里的返回值
  'isEvenOrOdd' //对数据进行的过滤方法。
  ])
  ```
`  
#### 3.4 Mutation

> 官方文档中的描述是：更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数。

　　mutation的意思就是变化，代表着state的更改，利用mutation属性来注册更改的方法，定义state要进行怎样的更改。这也就与vuex简介中的描述“以相应的规则保证状态以一种可预测的方式发生变化”对应。
```
`const mutations={
//这里的increment就是事件类型，相当于自定义事件的事件名，在组件中@click=“increment”，但是这里的increment只是事件名，还不是调用，调用要用commit。
increment(state){
//这就是回调函数mutation handler，用来定义state要进行怎样的变化。
state.count++;
},
//这是另外一个按钮上的减数的变化。
decrement(state){
state.count--;
}
}
```
`mutation的函数中也可以传额外的参数(payload)，比如：
```
`mutations: {
  increment (state, n) {
state.count += n
  }
 }
 ```
` 一般来说，payload应该是个对象
 ```
` mutations: {
   increment (state, payload) {
 state.count += payload.amount
   }
 }
 ```
` payload在函数调用时进行赋值。
 
#### 调用
 
 1. store.commit
	 
要调用handler进行状态变化时，需要用相应的type调用store.commit方法：
```
`//没有payload
store.commit("invrement")
//包含payload
store.commit('increment', 10)
//payload是对象
store.commit('increment', {
amount: 10
})或者
store.commit({
type: 'increment',
amount: 10
})
```
` 2. 组件调用
	 
Vuex提供了mapMutations的方法用来获取mutations，与state和getters不同的是，import进去后关联到的地方是在methods上
```
`import { mapMutations } from 'vuex'
export default {
// ...
methods: {
...mapMutations([
]()'increment',//将this.increment()映射为this.$store.commit('increment')
// `mapMutations` 也支持载荷：
 'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
])
}
}
 ```
` 
 Mutation必须是同步函数，不能用异步操作。如果需要进行异步的state改变，就需要用到下面要讲的Action。
 
#### 3.4.1工程上的常用操作----使用常量替代Mutation事件类型
 
> 官方文档：使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式。这样可以使 linter 之类的工具发挥作用，同时把这些常量放在单独的文件中可以让你的代码合作者对整个 app 包含的 mutation 一目了然：
```
`// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
```
`************* ```
`// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
// 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
[SOME_MUTATION]() (state) {
  // mutate state
}
  }
})
```
`#### 3.5 Action

> 官方文档：Action 类似于 mutation，不同在于：
> - Action 提交的是 mutation，而不是直接变更状态。
> - Action 可以包含任意异步操作。\</br\>
　　action翻译过来就是行为，主要对应的就是行为层的操作，Action不仅可以进行同步操作，也可以进行异步操作。同时还可以对mutations里定义的改变方式进行条件判断等过滤行为。
简单的acitons属性定义如下：
```
`const actions = {
//ES6参数解构简化代码，这里的increment是actions的函数名，用以在组件中调用，并非mutations里注册的函数名type。
increment({commit,state}){
//提交一个type为increment的mutations
commit('increment');
}
}
```
`
如果要对变化进行某些条件判断，在actions里进行：

```
`const actions = {
increment({commit,state}){
if(state.count\>10){
commit('increment');
}
}
}
```
`异步操作例子：

```
`const actions = {
//同样是actions注册的行为函数名
incrementAsync({commit,state}){
//异步操作
var asyncFn = new Promise((resolve,reject) =\> {
setTimeout(() =\> {
resolve();
},1000);
});
asyncFn.then(() =\> {
  commit('increment');
}).catch(() =\> {
 console.log('Error');
   });
 }       
 }
```
`#### 分发Action（分发的英文就是diapatching）

1. store.dispatch

```
`//这里的increment是actions里的函数名，在actions里面的increment里commit的才是mutation的increment
store.dispatch('increment')
```
`
2. 组件中分发

Vuex提供了mapActions的方法用来获取actions， 与mapMutation类似，import进去后关联到methods中。

```
`methods:mapActions([
]()  'increment',
  'decrement',
  'incrementAsync'
  ])
```
`
#### 3.6 Module

　　当处理较大的项目时，状态有很多，全部集中在一个store里就会很臃肿，且不易查询。因此Vuex允许我们将store分割成模块(module)，就像是仓库中的货架，或者图书馆中的分馆一样，每个模块拥有自己的state、getter、mutation、action。甚至在模块中还可以嵌套子模块。
```
`const moduleA = {
state: { ... },
mutations: { ... },
actions: { ... },
getters: { ... }
}
const moduleB = {
state: { ... },
mutations: { ... },
actions: { ... }
}

const store = new Vuex.Store({
modules: {
a: moduleA,
 b: moduleB
  }
})

store.state.a // -\> moduleA 的状态
store.state.b // -\> moduleB 的状态
```
`
### 4. todolist例子

```
`//App.vue
\<template\>
  \<div id="app"\>
\<p\>
  \<label for=""\>todo:\</label\>
  \<input type="text" v-model="currentItem" @blur="currentItemChange"\>
  \<button @click="addItems"\> +\</button\>
\</p\>
\<my-list\>\</my-list\>
\<router-view\>\</router-view\>
  \</div\>
\</template\>

\<script\>
  import MyList from "./components/MyList"
  import {mapState} from 'vuex'

  export default {
name: 'App',
components: {
  MyList
},
data() {
  return {
activeIndex: "/todoList",
currentItem: ''
  }
},
computed: {
  ...mapState([
]()'todoItem'
  ])
},
methods: {
  currentItemChange () {
this.$store.commit('updateCurrentItem', this.currentItem)
  },
  addItems () {
this.$store.commit('addItems')
this.currentItem = ''
this.$store.commit('updateCurrentItem', '')
  }
}
  }
\</script\>

\<style\>
# app {
font-family: 'Avenir', Helvetica, Arial, sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
text-align: center;
color: #2c3e50;
position: relative;
  }

  body {
margin: 0;
padding: 0;
  }
\</style\>
```
`************* ************* ```
`//MyList.vue
\<template\>
  \<ul\>
\<li v-for="(item,index) in todoItem" :key="index"\>{{item.content}}
  \<button @click="deleteItems(index)"\> - \</button\>
\</li\>
  \</ul\>

\</template\>

\<script\>
  import {mapState, mapMutations} from 'vuex'
  export default {
name: "MyList",
computed: mapState(
  ['todoItem']()
),
methods:{
  ...mapMutations([
]()'deleteItems'
  ])
}
  }
\</script\>

\<style scoped\>

\</style\>
```
`
*************** *************** ```
`//store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const state = {
  todoItem:[
](){
  id:1,
  content:"learn vue"
},
{
  id:2,
  content:"learn vue-router"
},
{
  id:3,
  content:"learn vue-cli"
},
{
  id:4,
  content:"learn vuex"
}

  ],
  nextId:5,
  currentItem: ''
}

const mutations={
  addItems(state, item){
state.todoItem.push({
  content: state.currentItem,
  id: state.todoItem.length ? state.todoItem[state.todoItem.length - 1]().id + 1 : 1
})
  },
  deleteItems(state,index){
state.todoItem.splice(index,1)   
  },
  updateCurrentItem (state, cur) {
state.currentItem = cur
  }
}

export default new Vuex.Store({
  state,
  mutations
})
```
`
************* ************* 优化版：
```
`\<p\>
  \<label for=""\>todo:\</label\>
  \<input type="text" v-model="currentItem"\>
  \<button @click="addItems"\> +\</button\>
\</p\>
\<my-list\>\</my-list\>

computed: {
  ...mapState([
]()'todoItem'
  ])
},
methods: {
  addItems () {
this.$store.commit('addItems', {
  content: this.currentItem,
  id: this.todoItem.length ? this.todoItem[this.todoItem.length - 1]().id + 1 : 1
})
this.currentItem = ''
  }
}
```
`********
********
```
`const mutations={
  addItems(state, item){
let newItem = {};
state.todoItem.push(item)

  },
  deleteItems(state,index){
state.todoItem.splice(index,1)

  }
}
```
`
### 5. 项目结构

> 官方文档：Vuex 并不限制你的代码结构。但是，它规定了一些需要遵守的规则：
> 1. 应用层级的状态应该集中到单个 store 对象中。
> 2. 提交 mutation 是更改状态的唯一方法，并且这个过程是同步的。
> 3. 异步逻辑都应该封装到 action 里面。\</br\>
　　只要你遵守以上规则，如何组织代码随你便。如果你的 store 文件太大，只需将 action、mutation 和 getter 分割到单独的文件。
　　对于大型应用，我们会希望把 Vuex 相关代码分割到模块中。下面是项目结构示例：
```
`├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
├── index.js          # 我们组装模块并导出 store 的地方
├── actions.js        # 根级别的 action
├── mutations.js      # 根级别的 mutation
└── modules
├── cart.js       # 购物车模块
└── products.js   # 产品模块
````

