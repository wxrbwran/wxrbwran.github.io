---
title: Vue中的过渡效果
date: 2016-10-26 11:10:31
tags: vue
---

>  这里只写由css实现的简单过渡，js用到在做记录。

### 过渡效果

#### 概述

Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。
包括以下工具：

1. 在 CSS 过渡和动画中自动应用 class
2. 可以配合使用第三方 CSS 动画库，如 [Animate.css](https://daneden.github.io/animate.css/)
3. 在过渡钩子函数中使用 JavaScript 直接操作 DOM
4. 可以配合使用第三方 JavaScript 动画库，如 [Velocity.js](http://velocityjs.org/)


#### 单元素/组件的过渡

Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加 entering/leaving 过渡

 - 条件渲染 （使用 v-if）
 - 条件展示 （使用 v-show）
 - 动态组件
 - 组件根节点
这里是一个典型的例子：
```css
<div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>
</div>
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-active {
  opacity: 0
}
```
元素封装成过渡组件之后，在遇到插入或删除时，Vue 将自动嗅探目标元素是否有 CSS 过渡或动画，并在合适时添加/删除 CSS 类名。

如果过渡组件设置了过渡的 JavaScript 钩子函数，会在相应的阶段调用钩子函数。

如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作（插入/删除）在下一帧中立即执行。(注意：此指浏览器逐帧动画机制，与 Vue，和Vue的 nextTick 概念不同)

#### 过渡的-CSS-类名

会有 4 个(CSS)类名在 enter/leave 的过渡中切换

**v-enter**: 定义进入过渡的开始状态。在元素被插入时生效，在下一个帧移除。

**v-enter-active**: 定义进入过渡的结束状态。在元素被插入时生效，在 transition/animation 完成之后移除。

**v-leave**: 定义离开过渡的开始状态。在离开过渡被触发时生效，在下一个帧移除。

**v-leave-active**: 定义离开过渡的结束状态。在离开过渡被触发时生效，在 transition/animation 完成之后移除。

![Transition Diagram](https://vuefe.cn/images/transition.png)

对于这些在 enter/leave 过渡中切换的类名，v- 是这些类名的前缀。使用 <name="my-transition>" 可以重置前缀，比如 v-enter 替换为 my-transition-enter。

v-enter-active 和 v-leave-active 可以控制 进入/离开 过渡的不同阶段，在下面章节会有个示例说明。

### CSS 过渡

**常用的过渡都是使用 CSS 过渡。**

下面是一个简单例子：
```css
<div id="example-1">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition name="slide-fade">
    <p v-if="show">hello</p>
  </transition>
</div>
new Vue({
  el: '#example-1',
  data: {
    show: true
  }
})
/* 可以设置不同的进入和离开动画 */
/* 设置持续时间和动画函数 */
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-active {
  padding-left: 10px;
  opacity: 0;
}
```

#### CSS 动画

CSS 动画用法同 CSS 过渡，区别是在动画中 v-enter 类名在节点插入 DOM 后不会立即删除，而是在 animationend 事件触发时删除。

示例： (省略了兼容性前缀)
```css
<div id="example-2">
  <button @click="show = !show">Toggle show</button>
  <transition name="bounce">
    <p v-if="show">Look at me!</p>
  </transition>
</div>
new Vue({
  el: '#example-2',
  data: {
    show: true
  }
})
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-out .5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(0);
  }
}
```
#### 自定义过渡类名

我们可以通过以下特性来自定义过渡类名：

**enter-class**
**enter-active-class**
**leave-class**
**leave-active-class**
他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如** Animate.css **结合使用十分有用。

示例：
```css
<link href="https://unpkg.com/animate.css@3.5.1/animate.min.css" rel="stylesheet" type="text/css">
<div id="example-3">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition
    name="custom-classes-transition"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
</div>
new Vue({
  el: '#example-3',
  data: {
    show: true
  }
})
```
<!--more-->

#### 同时使用 Transitions 和 Animations

Vue 为了知道过渡的完成，必须设置相应的事件监听器。它可以是 transitionend 或 animationend ，这取决于给元素应用的 CSS 规则。如果你使用其中任何一种，Vue 能自动识别类型并设置监听。

但是，在一些场景中，你需要给同一个元素同时设置两种过渡动效，比如 animation 很快的被触发并完成了，而 transition 效果还没结束。在这种情况中，你就需要使用 type 特性并设置 animation 或 transition 来明确声明你需要 Vue 监听的类型。

#### 初始渲染的过渡

可以通过 **appear** 特性设置节点的在初始渲染的过渡
```
<transition appear>

</transition>
```
这里默认和进入和离开过渡一样，同样也可以自定义 CSS 类名。
```html
<transition
  appear
  appear-class="custom-appear-class"
  appear-active-class="custom-appear-active-class"
>

</transition>
```

#### 多个元素的过渡

我们之后讨论 **多个组件的过渡**, 对于原生标签可以使用 v-if/v-else 。最常见的多标签过渡是一个列表和描述这个列表为空消息的元素：
```
<transition>
  <table v-if="items.length > 0">

  </table>
  <p v-else>Sorry, no items found.</p>
</transition>
```
可以这样使用，但是有一点需要注意：

当有相同标签名的元素切换时，需要通过 key 特性设置唯一的值来标记以让 Vue 区分它们，否则 Vue 为了效率只会替换相同标签内部的内容。即使在技术上没有必要，给在 <transition> 组件中的多个元素设置 key 是一个更好的实践。
##### 示例:
```
<transition>
  <button v-if="isEditing" key="save">
    Save
  </button>
  <button v-else key="edit">
    Edit
  </button>
</transition>
```
在一些场景中，也可以给通过给同一个元素的 key 特性设置不同的状态来代替 v-if 和 v-else，上面的例子可以重写为：
```
<transition>
  <button v-bind:key="isEditing">
    {{ isEditing ? 'Save' : 'Edit' }}
  </button>
</transition>
```

使用多个 v-if 的多个元素的过渡可以重写为绑定了动态属性的单个元素过渡。 例如：
```
<transition>
  <button v-if="docState === 'saved'" key="saved">
    Edit
  </button>
  <button v-if="docState === 'edited'" key="edited">
    Save
  </button>
  <button v-if="docState === 'editing'" key="editing">
    Cancel
  </button>
</transition>
```

可以重写为：
```
<transition>
  <button v-bind:key="docState">
    {{ buttonMessage }}
  </button>
</transition>

computed: {
  buttonMessage: function () {
    switch (docState) {
      case 'saved': return 'Edit'
      case 'edited': return 'Save'
      case 'editing': return 'Cancel'
    }
  }
}
```
### [过渡模式](https://vuefe.cn/guide/transitions.html#过渡模式)
**同时生效**的进入和离开的过渡不能满足所有要求，所以 Vue 提供了 **过渡模式**

 - **in-out**: 新元素先进行过渡，完成之后当前元素过渡离开。
 - **out-in**: 当前元素先进行过渡，完成之后新元素过渡进入。

用 **out-in** 重写之前的开关按钮过渡：
```
<transition name="fade" mode="out-in"></transition>
```

只用添加一个简单的特性，就解决了之前的过渡问题而无需任何额外的代码。
**in-out** 模式不是经常用到，但对于一些稍微不同的过渡效果还是有用的。

### 多个组件的过渡

多个组件的过渡很简单很多,我们不需要使用 key 特性。相反，我们只需要**使用动态组件**:
#### 关于动态组件
>  多个组件可以使用同一个挂载点，然后动态地在它们之间切换。使用保留的 <component> 元素，动态地绑定到它的 is 特性：
```
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
<component v-bind:is="currentView">
  <!-- 组件在 vm.currentview 变化时改变！ -->
</component>
也可以直接绑定到组件对象上：

var Home = {
  template: '<p>Welcome home!</p>'
}
var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

使用动态组件实现过渡
```
<transition name="component-fade" mode="out-in">
  <component v-bind:is="view"></component>
</transition>
new Vue({
  el: '#transition-components-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>'
    },
    'v-b': {
      template: '<div>Component B</div>'
    }
  }
})
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-active {
  opacity: 0;
}
```
[更多动画效果请参考这里](https://vuefe.cn/guide/transitions.html)