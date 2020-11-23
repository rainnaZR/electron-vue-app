<template>
  <div id="app">
    <Header />
    <!-- 不规则窗口 -->
    <CircleElement />
    <div class="fonts">
      <div style="font:caption">我是标题</div>
      <div style="font:menu">我是菜单</div>
      <div style="font:message-box">我是对话框中的字体</div>
      <div style="font:status-bar">我是状态栏中的字体</div>
    </div>
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="123123Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Header from './components/Header';
import CircleElement from './components/CircleElement';
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    Header,
    CircleElement,
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
