<template>
    <div class="app-layout">
      <div class="sidebar">
        <SidebarSection/>
      </div>

      <div class="main-content">
        <nuxt />
      </div>
    </div>
</template>

<script>
import sidebar from '~/components/sidebar.vue'

export default {
    components: {
      SidebarSection: sidebar
    }
}

</script>

<style lang="scss">

html,body,h1,h2,h3,p,ul,li {
  margin: 0;
  padding: 0;
  line-height: 1.0;
}

a {
  text-decoration: none;
  color: inherit
}

ul {
  list-style: none;
}

// reset css 

html {
  font-size: 62.5%;
}

body {
  font-size: 1.6rem;
  color: #333;
  font-family: 'Noto Sans JP', sans-serif;
}

.app-layout {
  display: flex;
}

.sidebar {
  width: 300px;
  background: #0f4c75;
  color: #fff;
  padding: 20px;
  box-sizing: border-box;
  height: 100vh;
}

.main-content {
 width: 100%;
 background: #bbe1fa;
 height: 100vh;
}

</style>


