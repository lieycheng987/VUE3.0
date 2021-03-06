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
>> ### vue2.0中
>> 通过el拿到挂载的组件，通过obiect.forEach遍历拿到每个值，然后通过obj.defineProperty（重新定义key）set函数设置新的值，通过个get函数返回  
>> 通过发布者订阅者模式，告知其他，通过数组加入所有订阅者信息，监听函数发生改变就通知所有的订阅这改变（一个属性对应一个监听对象，）
>> ### vue3.0JS Proxy(代理)
>>`let p = new Proxy(targrt,handler)`
>>`target`使用`Proxy`包装的对象(任意类型)  
>>`handler`对象，其属性是当前执行一个操作时定义代理的行为的函数
>>
```
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
可以设置一个私有变量
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
### 响应性基础
可以使用`reactive`方法
```
import { reactive } from 'vue'

// 响应式状态
const state = reactive({
  count: 0
})
```
reactive 相当于 Vue 2.x 中的 Vue.observable() API ，为避免与 RxJS 中的 observables 混淆因此对其重命名。其相当于一个Proxy对象，该 API 返回  
一个响应式的对象状态。该响应式转换是“深度转换”——它会影响嵌套对象传递的所有 property。Vue 中响应式状态的基本用例是我们可以在渲染期间使用它。因为  
依赖跟踪的关系，当响应式状态改变时视图会自动更新。  
这就是 Vue 响应性系统的本质。当从组件中的 data() 返回一个对象时，它在内部交由 reactive() 使其成为响应式对象。模板会被编译成能够使用这些响应式  
`property` 的渲染函数。
#### 创建独立的响应式值作为`refs`
想象一下，我们有一个独立的原始值 (例如，一个字符串)，我们想让它变成响应式的。当然，我们可以创建一个拥有相同字符串 property 的对象，并将其传递给   
reactive。Vue 为我们提供了一个可以做相同事情的方法 ——ref：
```
import { ref } from 'vue'

const count = ref(0)
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```
#### 使用`readonly`防止更改响应式对象只读属性 吧
有时我们想跟踪响应式对象 (ref 或 reactive) 的变化，但我们也希望防止在应用程序的某个位置更改它。例如，当我们有一个被 provide 的响应式对象时，我  
们不想让它在注入的时候被改变。为此，我们可以基于原始对象创建一个只读的 Proxy 对象：
```
import { reactive, readonly } from 'vue'

const original = reactive({ count: 0 })

const copy = readonly(original)

// 在copy上转换original 会触发侦听器依赖

original.count++

// 转换copy 将导失败并导致警告
copy.count++ // 警告: "Set operation on key 'count' failed: target is readonly."
```
## 渲染函数
  当浏览器读取代码时会建立一个DOM树的结点来追踪所有内容，而VUE通过建立虚拟的DOM树来追踪真实的DOM节点 ，`h()`函数返回的不是DOM元素本身，更准确   
  名字可能是createNodeDescription ，它所包含的信息会告诉Vue页面上需要渲染什么样的节点，包括及其子结点的描述信息。所以等于说`h()`函数返回的是  
  “虚拟节点”，称作`VNode`，虚拟DOM是我们对vue组件书建立起来的整个VNode树的称呼，而`h()`函数是用来创建Vnode的实用程序。其内部接受三个参数，分  
  别是html的标签名例如div，Object(对象或者一个组件)，Function(异步组件或者是某个函数)，在或者是空的程序详情见下列
  ```
  // @returns {VNode}
h(
  // {String | Object | Function | null} tag
  // 一个 HTML 标签名、一个组件、一个异步组件，或者 null。
  // 使用 null 将会渲染一个注释。
  //
  // 必需的。
  'div',

  // {Object} props
  // 与 attribute、prop 和事件相对应的对象。
  // 我们会在模板中使用。
  //
  // 可选的。
  {},

  // {String | Array | Object} children
  // 子 VNodes, 使用 `h()` 构建,
  // 或使用字符串获取 "文本 Vnode" 或者
  // 有 slot 的对象。
  //
  // 可选的。
  [
    'Some text comes first.',
    h('h1', 'A headline'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
```
所以我们可以创建想要的实列
```
const app = Vue.createApp({})

/** Recursively get text from children nodes */
function getChildrenTextContent(children) {
  return children
    .map(node => {
      return typeof node.children === 'string'
        ? node.children
        : Array.isArray(node.children)
        ? getChildrenTextContent(node.children)
        : ''
    })
    .join('')
}

app.component('anchored-heading', {
  render() {
    // create kebab-case id from the text contents of the children
    const headingId = getChildrenTextContent(this.$slots.default())
      .toLowerCase()
      .replace(/\W+/g, '-') // replace non-word characters with dash
      .replace(/(^-|-$)/g, '') // remove leading and trailing dashes

    return Vue.h('h' + this.level, [
      Vue.h(
        'a',
        {
          name: headingId,
          href: '#' + headingId
        },
        this.$slots.default()
      )
    ])
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```
上面的程序中`getChildrenTextContent`函数其中判断`node.children`是否为字符串类型，如果不是继续判断是否是数组类型，如果是递归判断其子节点(类  
似于如果满足组条件递归判断其子节点的各个方面一直往下判断类似与虚拟dom节点反复判断)，再将最后的如果是数组型的添加为字符串类型`render()`函数中  
参数传入`$slot.default`此函数意味着拿到  所有非具名插槽的元素，再把所有字符串通过`.toLowerCase()`（`toUpperCase()`能全部转为大写）函数全部  
转换为小写分别往里面进行添加在进行正则替换，在返回虚拟dom的节点从h标签开始分别进行渲染
