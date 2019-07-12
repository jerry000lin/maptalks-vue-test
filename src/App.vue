<template>
  <div id="app">
    <div class="position">
      <router-view style="height=100%;" />
    </div>
  </div>
</template>

<style lang="less">
html,
body {
  padding: 0;
  margin: 0;
}
#app {
  width: 100%;
  height: 100%;
  .position {
    position: fixed;
    height: 100%;
    width: 100%;
  }
}
</style>
