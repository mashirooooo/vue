### vue 
  # (1)、状态提升 将数据放在他们共同父级组件管理 跨级组件处理
  # (2)、eventbus 将数据 ： 1、import Vue from 'vue' export const EventBus = new Vue() 
  # vue.$on('eventName', eventHandleFunction) 定义事件 vue.$emit('eventName', arguments)
  # 在main.js Vue.prototype.$EventBus = new Vue() 定义eventbus
  # (3)、ref链 $parent / $children
  # (4)、$attrs/$listeners $attrs：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件。通常配合 inheritAttrs 选项一起使用。 基本很少用到 处理跨级组件
  # (5)、provide/inject 父组件  provide: {name：'xx} 子组建件 inject: ['name' ] 注意这不是响应的 需要将属性变成可响应的 用 2.6 vue.observable(监听对象)  否则将this 当前实例传下去也是可以的
  # (6)、vuex 1.简要介绍Vuex原理Vuex实现了一个单向数据流，在全局拥有一个State存放数据，当组件要更改State中的数据时，必须通过Mutation进行，Mutation同时提供了订阅者模式供外部插件调用获取State数据的更新。而当所有异步操作(常见于调用后端接口异步获取更新数据)或批量的同步操作需要走Action，但Action也是无法直接修改State的，还是需要通过Mutation来修改State的数据。最后，根据State的变化，渲染到视图上。
  # 2.简要介绍各模块在流程中的功能：
  # Vue Components：Vue组件。HTML页面上，负责接收用户操作等交互行为，执行dispatch方法触发对应action进行回应。
  # dispatch：操作行为触发方法，是唯一能执行action的方法。
  # actions：操作行为处理模块,由组件中的$store.dispatch('action 名称', data1)来触发。然后由commit()来触发mutation的调用 , 间接更新 state。负责处理Vue Components接收到的所有交互行为。包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。
  # commit：状态改变提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。
  # mutations：状态改变操作方法，由actions中的commit('mutation 名称')来触发。是Vuex修改state的唯一推荐方法。该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。
  # state：页面状态管理容器对象。集中存储Vue components中data对象的零散数据，全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，利用Vue的细粒度数据响应机制来进行高效的状态更新。
  # getters：state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。
  # 3.Vuex与localStorage
  # vuex 是 vue 的状态管理器，存储的数据是响应式的。但是并不会保存起来，刷新之后就回到了初始状态，具体做法应该在vuex里数据改变的时候把数据拷贝一份保存到localStorage里面，刷新之后，如果localStorage里有保存的数据，取出来再替换store里的state。

  # vuex是单一状态树 可以借助开发工具查看不同状态 但是如果不通过commit更改state数据，vuex将不会记录该改动，虽然是还可以更改数据的，严格模式下还会console.error显示错误
