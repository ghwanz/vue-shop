<template>
  <div id="app">
   
    <el-container class="container">
      <el-aside width="70px">
        <leftNav/>
      </el-aside>
      <el-main class="main">        
        <router-view/>
      </el-main>
    </el-container>

  </div>
</template>

<script>
import leftNav from '@/components/common/leftNav';

export default {
  name: 'App',
  components:{
    leftNav
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  height: 100%;
}

.container{
    align-items: stretch;
    height: 100%;
}

.main{
  padding: 0;
}

</style>
