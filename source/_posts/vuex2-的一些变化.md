---
title: vuex2 的一些变化
date: 2016-10-28 17:46:15
tags: vue
---

`` $store.state.count ``可以直接获取Store中的状态参数，但是getters可以返回对参数进行处理后的结果。
``mutations``可以直接使用，但必须为同步方法。如果想使用异步方法，请使用``actions``，actions异步同步方法都可以。
``import { mapGetters, mapActions,mapMutations } from 'vuex'``提供了使用方法的简便模式
{%  codeblock lang:javascript Store %}
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

// root state object.
// each Vuex instance is just a single state tree.
const state = {
  count: 0
}

// mutations are operations that actually mutates the state.
// each mutation handler gets the entire state tree as the
// first argument, followed by additional payload arguments.
// mutations must be synchronous and can be recorded by plugins
// for debugging purposes.必须为同步方法
const mutations = {
  increment (state) {
    state.count++
  },
  decrement (state,amount) {
    state.count-= amount
  }
}

// actions are functions that causes side effects and can involve
// asynchronous operations. 可以加入异步方法
const actions = {
  // increment: ({ commit }) => commit('increment'),
  // decrement: ({ commit }) => commit('decrement'),
  incrementIfOdd ({ commit, state }) {
    if ((state.count + 1) % 2 === 0) {
      commit('increment')
    }
  },
  incrementAsync ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('increment')
        resolve()
      }, 1000)
    })
  }
}

// getters are functions
const getters = {
  evenOrOdd: state => state.count % 2 === 0 ? 'even' : 'odd'
}

// A Vuex instance is created by combining the state, mutations, actions,
// and getters.
export default new Vuex.Store({
  state,
  getters,
  actions,
  mutations
})
{% endcodeblock %}
<!--more-->
{%  codeblock lang:html Vue组件 %}
<template>
  <div id="app">
    \为转义字符，请忽略
    Clicked: \{\{ $store.state.count \}\} times, count is \{\{ evenOrOdd \}\}.
    <button @click="increment">+</button>
    <button @click="decrement(2)">-</button>
    <button @click="incrementIfOdd">Increment if odd</button>
    <button @click="incrementAsync">Increment async</button>
  </div>
</template>

<script>
import { mapGetters, mapActions,mapMutations } from 'vuex'
export default {
  computed: mapGetters([
    'evenOrOdd'
  ]),
  methods: {
    ...mapMutations([
      'increment',
      'decrement',
    ]),
    ...mapActions([
      'incrementIfOdd',
      'incrementAsync'
    ])
  }
}
</script>

{% endcodeblock %}
{%  codeblock lang:javascript Vue实例 %}
import Vue from 'vue'
import Counter from './Counter.vue'
import store from './store'

new Vue({
  el: '#app',
  store,
  render: h => h(Counter)
});

{% endcodeblock %}