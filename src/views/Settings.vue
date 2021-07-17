<template lang="pug">
#settings
  b-container
    b-row.rowstyle
      b-col(cols='12')
        h2.pt-2.text-center Music
        b-table(:items='items' :fields='fields' @row-clicked='select')
          template(#cell(src)='data')
            audio(controls :src='require("../assets/"+data.value)')
          template(#cell(select)='data')
            font-awesome-icon(:icon='["fas", "check"]' v-if='sound === data.item.src') O
</template>

<script>
export default {
  name: 'Settings',
  data () {
    return {
      items: [
        { name: 'Ring', src: 'alarm.mp3' },
        { name: 'Yay', src: 'yay.mp3' }
      ],
      fields: [
        { key: 'name', label: 'Music' },
        { key: 'src', label: 'Listening' },
        { key: 'select', label: 'Choice' }
      ]
    }
  },
  computed: {
    sound () {
      return this.$store.state.sound
    }
  },
  methods: {
    select (item) {
      this.$store.commit('selectSound', item.src)
    }
  }
}
</script>

<style scoped>
#settings{
  height: 120vh;
  background: #E6E3D9;
}

.container{
  height: 100%;
  background-image: url(../assets/tomatobg.png);
  background-size: 25%;
  background-repeat:space;
  background-position:center;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

h2{
  color: #981E23;
}

.rowstyle{
  margin-top: 2rem;
  background: rgb(245, 245, 245);
  width: 80%;
  height: 90%;
}

</style>
