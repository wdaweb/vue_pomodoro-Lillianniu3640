<template lang="pug">
#app
  b-navbar(toggable='lg' type='light' variant='faded')
    b-container
      b-navbar-brand(to='/') <b-img src="./assets/logo.png" style="width:15%"></b-img>
      b-navbar-toggle(target='navbar-collapse')
      b-collapse#navbar-collapse(is-nav)
        b-navbar-nav.ml-auto
          b-nav-item(to='/list')
            font-awesome-icon(:icon='["fas", "clipboard"]')
          b-nav-item(to='/settings')
            font-awesome-icon(:icon='["fas", "music"]')
  hr
  keep-alive
    router-view
</template>

<style scoped>
#app{
  height: 100vh;
  background: #E6E3D9;
}

.navbar{
  height: 8%;
}

hr{
  border: 2.5px dotted #981E23;
  margin: 0;
}
</style>
