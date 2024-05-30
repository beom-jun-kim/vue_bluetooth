<template>
  <BluetoothCom msg="Welcome to Your Vue.js App"/>
</template>

<script>
import BluetoothCom from './components/BluetoothCom.vue'

export default {
  name: 'App',
  components: {
    BluetoothCom
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
</style>./components/BluetoothCom.vue
