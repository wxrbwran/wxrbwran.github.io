---
title: vue-router2的一些变化
date: 2016-10-28 17:23:32
tags: Vue
---
[vue-router2详细文档](http://router.vuejs.org/zh-cn/index.html)
v-link由router-link替换，router-view 的name属性，可以匹配多个路由component**(s)**
~~hashbang 弃用~~
router.map,router.start,router.redirect，saveScrollPosition等都变为router的实例属性
router.go()->router.push()
{% codeblock lang:"html" router %}
<p style="display: flex;justify-content:space-around;
    align-items:center;flex-flow:row wrap;align-content:flex-start;">
    <router-link to="/foo">go to foo</router-link>
    <router-link to="/bar">go to bar</router-link>
    <router-link to="/user/dxf">go to user:dxf</router-link>
    <router-link to="/user/wxr/profile">go to wxr profile</router-link>
    <router-link to="/user/wxr/posts">go to wxr posts</router-link>
    <router-link to="/multi">go to multi</router-link>
  </p>
  <transition name="fade" mode="out-in">
    <keep-alive>
      <router-view name="default"></router-view>
      <router-view name="r2"></router-view>
    </keep-alive>
  </transition>
{% endcodeblock %}
<!--more-->
{% codeblock lang:"javascript" router %}

import vueRouter from 'vue-router'
import {mapState} from 'vuex'
import {mapGetters} from 'vuex'
import {mapActions} from 'vuex'

const Foo = resolve => {
  // require.ensure 是 Webpack 的特殊语法，用来设置 code-split point
  // （代码分块）
  require.ensure(['../components/Foo.vue'], () => {
    resolve(require('../components/Foo.vue'))
  })
};
const Bar = resolve => {
  require.ensure(['../components/Bar.vue'], () => {
    resolve(require('../components/Bar.vue'))
  })
};
const User = {
  template: `<section>
  <h3>{{$store.state.count}} is {{evenOrOdd}}</h3>
  <div @click="increment">User:{{ $route.params.name }}</div>
  <router-view></router-view>
</section>`,
  computed: mapGetters([
    'evenOrOdd'
  ]),
  methods: mapActions([
    'increment'
  ])
}
const Profile = {
  template: '<div @click="increment">profiles,and num is {{$store.state.count}}</div>',
  methods: mapActions([
    'increment'
  ])
}
const UserPosts = {template: '<div @click="alertCount">posts</div>',
  methods:mapActions([
    'alertCount'
  ])
}
const UserHome = {template: '<div>homePage</div>'}


const router = new vueRouter({
  mode: 'hash',//history
  routes:[
    {path: '/foo', component: Foo},
    {path: '/bar', component: Bar},
    {
      path: '/multi', components: {
      default: Foo,
      r2: Bar
      }
    },
    {
      path: '/user/:name', component: User, name: 'user',
      children: [
        {
          path: '', component: UserHome,
          beforeEnter: (to, from, next) => {
            console.log('enter user homepage');
            next()
          }
        },
        {
          path: 'profile', component: Profile,
          beforeEnter: (to, from, next) => {
            console.log('enter user profile');
            next()
          }
        },
        {path: 'posts', component: UserPosts},
        {path: 'post',redirect: 'posts'}
      ]
    },
  ],
  linkActiveClass: 'current'
});
export default router
{% endcodeblock %}
{% codeblock lang:"javascript" Vue实例 %}
import  Vue from 'vue'
import vueRouter from 'vue-router'
import Vuex from 'vuex'

import store from './vuex/store'
import router from './route/routers'
Vue.use(vueRouter);
Vue.use(Vuex);
//异步懒加载

const app = new Vue({
  router,
  store,
  mounted: function () {
    console.log('mounted...')
    console.log(this.$router)
  },
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
      console.log('to', to)
      console.log('from', from)
    }
  }
}).$mount('#app');
router.beforeEach((to, from, next) => {
  console.log('before each')
  next()
});
router.push('/user/sss/posts');

{% endcodeblock %}