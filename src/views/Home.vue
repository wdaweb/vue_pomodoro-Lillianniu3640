<template lang="pug">
#home
  b-container
    b-row
      b-col(cols='6').mt-5
        b-row
          b-col(cols="12")
            h1.clocktext {{ timeText }}
          b-col(cols="12").starttitle
            b-row
              b-col(cols="7")
                h3.text-center {{ currentText }}
              b-col(cols="5")
                b-btn(variant='outline-danger' pill v-if='status !== 1' @click='start').mr-2
                  font-awesome-icon(:icon='["fas", "play"]')
                b-btn(variant='outline-danger' pill v-if='status === 1' @click='pause').mr-2
                  font-awesome-icon(:icon='["fas", "pause"]')
                b-btn(variant='outline-dark' pill v-if='current.length > 0' @click='finish(true)')
                  font-awesome-icon(:icon='["fas", "step-forward"]')
      b-col(cols='6').addtask
        b-row
        b-form-group(label='' :state='state')
          b-row
            b-col(cols="10")
              b-form-input(v-model='newitem' placeholder='Add New Task' :state='state' trim @keydown.enter='additem')
            b-col(cols="2")
              b-btn(variant='dark' @click='additem') +
            b-col(cols="12")
              hr
              h4.ml-2 To Do List
              b-table.tablestyle(:items='list' :fields='listfields')
                template(#cell(name)='data')
                  b-form-input(
                    v-if='data.item.edit'
                    v-model='data.item.model'
                    trim
                    :state='data.item.state'
                    @keydown.enter='changelist(data.index)'
                    @keydown.esc='cancellist(data.index)'
                  )
                  span(v-else) {{ data.value }}
                template(#cell(action)='data')
                  span(v-if='!data.item.edit')
                    b-btn(@click='editlist(data.index)' variant='faded')
                      font-awesome-icon(:icon='["fas", "pen-nib"]')
                    b-btn.ml-2.fontcolor(@click='dellist(data.index)' variant='faded')
                      font-awesome-icon(:icon='["fas", "trash"]')
                  span(v-else)
                    b-btn(@click='changelist(data.index)' variant='faded')
                      font-awesome-icon(:icon='["fas", "check"]')
                    b-btn.ml-2(@click='cancellist(data.index)' variant='faded')
                      font-awesome-icon(:icon='["fas", "undo"]')
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      timer: 0,
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
    status () {
      return this.$store.state.status
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
    current () {
      return this.$store.state.current
    },
    currentText () {
      let text = this.current
      if (text.length === 0) {
        if (this.list.length === 0) {
          text = 'No Tomato.'
        } else {
          text = 'Start'
        }
      }
      return text
    },
    timeleft () {
      return this.$store.state.timeleft
    },
    timeText () {
      const m = Math.floor(this.timeleft / 60)
      const s = Math.floor(this.timeleft % 60)
      return `${m} : ${s}`
    }
  },
  methods: {
    pause () {
      clearInterval(this.timer)
      this.$store.commit('changeStatus', 2)
    },
    start () {
      if (this.status !== 2 && this.list.length > 0) {
        this.$store.commit('start')
      }
      if (this.current.length > 0) {
        this.$store.commit('changeStatus', 1)
        this.timer = setInterval(() => {
          this.$store.commit('countdown')
          if (this.timeleft <= -1) {
            this.finish(false)
          }
        }, 1000)
      }
    },
    finish (skip) {
      clearInterval(this.timer)
      this.$store.commit('changeStatus', 0)
      this.$store.commit('addFinish')

      if (!skip) {
        const audio = new Audio()
        audio.src = require('../assets/' + this.$store.state.sound)
        audio.play()
      }

      if (this.list.length > 0) {
        this.start()
      } else {
        this.$swal({
          icon: 'success',
          title: 'Done'
        })
      }
    },
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
#home{
  height: 85vh;
}

.container{
  height: 100%;
  background-image: url(../assets/tomatobg.png);
  background-size: 35%;
  background-repeat:no-repeat;
  background-position:5vw 15vh;
  position: relative;
}

.clocktext{
  font-size: 5rem;
  position: absolute;
  top:0;
  left:0;
  transform: translate(9rem, 12rem);
}

.starttitle{
  position: absolute;
  top:0;
  left:0;
  transform: translate(0rem, 28rem);
}

.addtask{
  margin-top: 5rem;
}

.form-control{
  border-radius:1.25rem;
  margin-bottom: 1rem;
  width:100%;
}

h4{
  color: #981E23;
}

.tablestyle{
  background: white;
  box-shadow: 0px 4px 8px 2px rgba(59, 59, 59, 0.1);
}

.fontcolor{
  color: #981E23;
}

</style>
