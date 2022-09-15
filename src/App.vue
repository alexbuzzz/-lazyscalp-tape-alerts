<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import impuls from './components/impuls.vue'
import { appWindow } from '@tauri-apps/api/window'

</script>

<script>
  export default {
    methods: {
      closeWindow() {
        appWindow.close()
      }
    }
  }
</script>

<template>
  <div class="container-fluid p-0">

    <div @click="closeWindow()" class="titlebar-button">
      <span>×</span>
    </div>

    <div data-tauri-drag-region class="drag">
      ░
    </div>

    <impuls/>
  
    
  </div>

  

</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #d3d3d3;
  background-color: #333333;
  min-height: 100vh;
  overflow: hidden;
}
.titlebar-button {
  display: inline-flex;
  position: absolute;
  top: 0px;
  right: 0px;
  justify-content: center;
  align-items: center;
  width: 30px;
  height: 30px;
  color: #e0e0e0;
  z-index: 100;
}
.titlebar-button:hover {
  background: #404040;
  z-index: 100;
}
.drag {
  cursor: pointer;
  display: inline-flex;
  position: absolute;
  top: 28px;
  right: 10px;
  /* width: 40px; */
  /* height: 30px; */
  color: #d3d3d3;
  z-index: 1000;
}
::-webkit-scrollbar {
  width: 5px;
}

/* Track */
::-webkit-scrollbar-track {
  background-color: #303030;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #767676;
  border-radius: 5px;
}
</style>
