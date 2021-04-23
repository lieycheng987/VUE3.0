## v-on的相关修饰符
```
<!-- 阻止单击事件继续传播 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>
```
使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用`v-on:click.prevent.self`会阻止所有的点击，而`v-on:click.self.prevent`只会阻止对元素自身的点击。

## 按键修饰

`<input @keyup.enter="submit" />`  
- .enter
- .tab
- .delete (捕获“删除”和“退格”键)
- .esc
- .space
- .up
- .down
- .left
- .right



> 
> # VUE3.0过度动画
>>在插入、更新或从 DOM 中移除项时，Vue 提供了多种应用转换效果的方法。这包括以下工具：  
1.自动为 CSS 转换和动画应用 class;  
2.集成第三方 CSS 动画库，例如 animate.css;  
3.在过渡钩子期间使用 JavaScript 直接操作 DOM；  
4.集成第三方 JavaScript 动画库。
## 单元素/组件过度
  > Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡  
   1.条件渲染 (使用 v-if)  
   2.条件展示 (使用 v-show)  
   3.动态组件  
   4.组件根节点  

 **典型例子**  
 
 ```
 <div id="demo">  
    <button @click="show = !show">
      Toggle
     </button>
     <transition name="fade">
       <p v-if="show">hello</p>
     </transition>
  </div>
      const Demo = {
 data() {
    return {
      show: true
    }
  }
}

Vue.createApp(Demo).mount('#demo')
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
 ```
 
 当插入或者删除包含 `transition` 组件时VUE能做出以下处理  
 1.自动嗅探目标元素是否应用了 CSS 过渡或动画，  
 2.如果是，在恰当的时机添加/删除 CSS 类名。如果过渡组件提供了 JavaScript 钩子函数 ，这些钩子函数将在恰当的时机被调用。  
 3.如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作 (插入/删除) 在下一帧中立即执行。(注意：此指浏览器逐帧动画机制，和 Vue 的 nextTick 概念不同

## 过度类  
1.`v-enter-from`：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。  
2.`v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。  
3.`v-enter-to`：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡/动画完成之后移除。  
4.`v-leave-from`：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。  
5.`v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。  
6.`v-leave-to`：离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被删除)，在过渡/动画完成之后移除。

## JavaScript 钩子  
可以在attribute中声明js的钩子  
```
<transition
  @before-enter="beforeEnter"
  @enter="enter"
  @after-enter="afterEnter"
  @enter-cancelled="enterCancelled"
  @before-leave="beforeLeave"
  @leave="leave"
  @after-leave="afterLeave"
  @leave-cancelled="leaveCancelled"
  :css="false"
>
  <!-- ... -->
</transition>
// ...
methods: {
  // --------
  // ENTERING
  // --------

  beforeEnter(el) {
    // ...
  },
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  enter(el, done) {
    // ...
    done()
  },
  afterEnter(el) {
    // ...
  },
  enterCancelled(el) {
    // ...
  },

  // --------
  // 离开时
  // --------

  beforeLeave(el) {
    // ...
  },
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  leave(el, done) {
    // ...
    done()
  },
  afterLeave(el) {
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled(el) {
    // ...
  }
}
```
 
## 高阶响应
> ### 深入响应性原理
> JavaScript作为单线程编译语言，通常无法完成响应式内容，想用 JavaScript 编写类似的内容  
> 需要做到一下几点  
>- 检测其中某一个值是否发生变化
> - 用跟踪 (track) 函数修改值
> - 用触发 (trigger) 函数更新为最新的值

>> ### JS Proxy(代理)
>>`let p = new Proxy(targrt,handler)`
>>`target`使用`Proxy`包装的对象(任意类型)  
>>`handler`对象，其属性是当前执行一个操作时定义代理的行为的函数
>>```
let test = {
    name: "小红"
  };
  test = new Proxy(test, {
    get(target, key) {
      console.log('获取了getter属性');
      return target[key];
    }
  });
  console.log(test.name);
 ```
 >>可以设置一个私有变量
 ```
 var target = {
   name: 'poetries',
   _age: 22
}

var logHandler = {
  get: function(target,key){
    if(key.startsWith('_')){
      console.log('私有变量age不能被访问')
      return false
    }
    return target[key];
  },
  set: function(target, key, value) {
     if(key.startsWith('_')){
      console.log('私有变量age不能被修改')
      return false
    }
     target[key] = value;
   }
} 
var targetWithLog = new Proxy(target, logHandler);
 
// 私有变量age不能被访问
targetWithLog.name; 
 
// 私有变量age不能被修改
targetWithLog.name = 'others';
```
### Reflect
Reflect是一个内置的对象，它提供拦截JavaScript操作的方法，这些方法与处理器对象的方法相同。Reflect不是一个函数对象，因此它是不可构造的。

#### Reflect.get(target, key, context)    
- target: 查找的对象  
- key: 查找的属性  
- context: 读取getter函数的上下文对象
#### Reflect.set(target, key, value, context)
- target: 要给哪个对象设置
- key: 要设置的属性
 value: 设置的值
- 返回值: boolean
#### Reflect.has(target, key) 方法检测 target对象上有么有 key这个属性 
返回值: boolean
```
const obj = {
      name: 'qiqingfu',
      a: 2
    }
    console.log(Reflect.has(obj, 'name'))  // true
    console.log(Reflect.has(obj, 'a'))     // true
    console.log(Reflect.has(obj, 'b'))     // false
 ```
其实 Reflect.has 和 in 操作符类似。
#### Reflect.defineProperty(target, key, decs)
和 Object.defineProperty(target, key, decs) 一样更推崇使用前者。

方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。
```
    const obj = {
      
    }
    Reflect.defineProperty(obj, 'a', {
      value: 1,
      configurable: true, // 可配置
      writable: true      // 可写
    })
 ```
 ### Proxy vs 原始标识
Proxy 的使用确实引入了一个需要注意的新警告：在身份比较方面，被代理对象与原始对象不相等
```
const obj = {}
const wrapped = new Proxy(obj, handlers)

console.log(obj === wrapped) // false
```
在大多数情况下，原始版本和包装版本的行为相同，但请注意，它们在依赖严格比对的操作下将是失败的，例如 .filter() 或 .map()。使用选项式 API 时，  
这种警告不太可能出现，因为所有响应式都是从 this 访问的，并保证已经是 Proxy。

### 侦听器
  每个组件实例都有一个相应的侦听器实例，该实例将在组件渲染期间把“触碰”的所有 property 记录为依赖项。  之后，当触发依赖项的 setter 时，它会  
  通知侦听器，从而使得组件重新渲染。将对象作为数据传递给组件实例时，Vue 会将其转换为 Proxy。这个 Proxy   
  使 Vue 能够在 property 被访问或修改时执行依赖项跟踪和更改通知。每个 property 都被视为一个依赖项。首次渲染后，组件将跟踪一组依赖列表——即在  
  渲染过程中被访问的 property。反过来，组件就成为了其每个 property 的订阅者。当 Proxy 拦截到 set 操作时，该 property 将通知其所有订阅的组件  
  重新渲染。

