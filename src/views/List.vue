<template lang="pug">
#list
  b-container
    b-row.rowstyle
      b-col.colstyle(col="6")
        font-awesome-icon.text-light.mb-2(:icon='["fas", "clock"]')
        h5.text-light 。 {{ showTime }}
      b-col(cols='6')
        h2.pt-2.text-center Done
        b-table-simple
          thead
            tr
              th Task Name
              th Delete
          tbody
            tr(v-for='(item, idx) in finished' :key='idx')
              td {{ item }}
              td
                b-btn.fontcolor(@click='delfinish(idx)' variant='faded')
                  font-awesome-icon(:icon='["fas", "trash"]')
</template>

<script>
export default {
  name: 'List',
  data () {
    return {
      newitem: '',
      listfields: [
        { key: 'name', label: 'Task Name' },
        { key: 'action', label: 'More' }
      ]
    }
  },
  computed: {
    state () {
      if (this.newitem.length === 0) {
        return null
      } else if (this.newitem.length < 2) {
        return false
      } else {
        return true
      }
    },
    list () {
      return this.$store.getters.list.map(item => {
        if (item.model.length < 2) {
          item.state = false
        } else {
          item.state = true
        }
        return item
      })
    },
    finished () {
      return this.$store.state.finished
    },
    showTime () {
      const n = new Date()
      const h = n.getHours()
      const m = n.getMinutes()
      const y = n.getFullYear()
      const mo = n.getMonth() + 1
      const nd = n.getUTCDate()
      return `${y} / ${mo} / ${nd} 。 ${h} : ${m}`
    }
  },
  methods: {
    additem () {
      if (this.state) {
        this.$store.commit('addList', this.newitem)
        this.newitem = ''
      } else {
        this.$swal({
          icon: 'error',
          title: 'Error',
          text: 'Please import more than two words.'
        })
      }
    },
    editlist (index) {
      this.$store.commit('editList', index)
    },
    changelist (index) {
      console.log(index)
      if (this.list[index].state) {
        this.$store.commit('changeList', index)
      }
    },
    cancellist (index) {
      this.$store.commit('cancelList', index)
    },
    dellist (index) {
      this.$store.commit('delList', index)
    },
    delfinish (index) {
      this.$store.commit('delFinish', index)
    }
  }
}
</script>

<style scoped>
#list{
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

.fontcolor{
  color: #981E23;
}

.colstyle{
  display: flex;
  justify-content: center;
  align-items: center;
  background: #981E23;
}

.rowstyle{
  margin-top: 2rem;
  background: rgb(245, 245, 245);
  width: 80%;
  height: 90%;
}

</style>
