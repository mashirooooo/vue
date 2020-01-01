## 创建型模式，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。
## 结构型模式，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。
## 行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。

# 发布订阅模式
  // 一个播放器类
    class Player {
      watchers = {}
      constructor() {
        // 初始化观察者列表
        this.watchers = {}
        // 模拟2秒后发布一个'play'事件
        setTimeout(() => {
          this._publish('play', true)
        }, 2000)

        // 模拟4秒后发布一个'pause'事件
        setTimeout(() => {
          this._publish('pause', true)
        }, 4000)
      }

      // 发布事件
      _publish(event, data) {
        if (this.watchers[event] && this.watchers[event].length) {
          this.watchers[event].forEach(callback => callback.bind(this)(data))
        }
      }

      // 订阅事件
      subscribe(event, callback) {
        this.watchers[event] = this.watchers[event] || []
        this.watchers[event].push(callback)
      }

      // 退订事件
      unsubscribe(event = null, callback = null) {
        // 如果传入指定事件函数，则仅退订此事件函数
        if (callback&&event) {
          if (this.watchers[event] && this.watchers[event].length) {
            this.watchers[event].splice(this.watchers[event].findIndex(cb => Object.is(cb, callback)), 1)
          }

        // 如果仅传入事件名称，则退订此事件对应的所有的事件函数
        } else if (event) {
          this.watchers[event] = []

        // 如果未传入任何参数，则退订所有事件
        } else {
          this.watchers = {}
        }
      }
    }

    // 实例化播放器
    const player = new Player()
    console.log(player)

    // 播放事件回调函数1
    const onPlayerPlay1 = function(data) {
      console.log('1: Player is play, the `this` context is current player', this, data)
    }

    // 播放事件回调函数2
    const onPlayerPlay2 = data => {
      console.log('2: Player is play', data)
    }

    // 暂停事件回调函数
    const onPlayerPause = data => {
      console.log('Player is pause', data)
    }

    // 加载事件回调函数
    const onPlayerLoaded = data => {
      console.log('Player is loaded', data)
    }

    // 可订阅多个不同事件
    player.subscribe('play', onPlayerPlay1)
    player.subscribe('play', onPlayerPlay2)
    player.subscribe('pause', onPlayerPause)
    player.subscribe('loaded', onPlayerLoaded)

    // 可以退订指定订阅事件
    player.unsubscribe('play', onPlayerPlay2)
    // 退订指定事件名称下的所有订阅事件
    player.unsubscribe('play')
    // 退订所有订阅事件
    player.unsubscribe()

    // 可以在外部手动发出事件（真实生产场景中，发布特性一般为类内部私有方法）
    player._publish('loaded', true)
# 中介者模式 Mediator Pattern:

# 代理模式 Proxy Pattern:
  ## 为其他对象提供一种代理以控制对这个对象的访问。代理模式使得代理对象控制具体对象的引用。代理几乎可以是任何对象：文件，资源，内存中的对象，或者是一些难以复制的东西。
    ES6 的 Proxy对象
    const target = { name: 'Billy Bob', age: 15 }; 
    const handler = { 
      get(target, key, proxy) { 
        const today = new Date(); 
        console.log(`GET request made for ${key} at ${today}`); 
        return Reflect.get(target, key, proxy); 
      },
      set(target, key, value, proxy) {
        return Reflect.set(target, key, value, proxy); 
      } 
    }; 
    const proxy = new Proxy(target, handler); 
    proxy.name