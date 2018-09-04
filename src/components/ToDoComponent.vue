<template>
<div>
    <br>
  <div class="text-center">
            <input class="form-control w-50 centerPos" type="text" v-bind:placeholder="info" id="taskInput">
            <hr>
  
            <button class="btn btn-secondary btn-xl" @click="createSome">
            <i class="fa fa-plus"></i>
            </button>
  
            <div class="list-group" v-if="view" v-for="(task,i) in tasks">
              <div class="card" v-if="task.displays" id="listPart">
  
                <div class="card-header text-left">
                  blablabla- {{i}}
                   <button class="btn btn-danger" @click="dropSome(i,this)" style="margin-left:200px;">
                    <i class="fa fa-times"></i>
                  </button>
                </div>
  
                <div class="card-body">
                  <blockquote class="blockquote mb-0 text-left">
                    <p>{{task.text}}</p>
                    <footer class="blockquote-footer text-left"><i>{{task.time}}</i></footer>
                  </blockquote>
                </div>
  
              </div>
            </div>
            </div>
  </div>
</template>

<script>
export default{
  data () { 
    return{
    tasks: [{
      text: '',
      time: '',
      displays: false
    }],
    view: false,
    err: false,
    info: 'Task...'
    }
  },
  methods: {
    createSome () {
      var inp = document.getElementById('taskInput')
      if (inp.value == '') {
        alert('Nothing to record!')
      } else {
        this.view = true
        for (let i = 0; i < this.tasks.length; i++) {
          if (this.tasks[i].text == inp.value) {
            this.err = true
            inp.value = ''

            inp.style.cssText = "border:2px solid red"
            this.info = 'This task already created'

            setTimeout(function() {
              this.info = 'Task...'
              inp.style.cssText = "border:1px solid grey"
            }.bind(this), 2500)
            break;
          }
        }
        if (this.err == false) {
          this.tasks.push({
            text: inp.value,
            time: new Date(),
            displays: true
          })
          inp.value = ''
        }
      }
      this.err = false
    },
    dropSome (i) {
      this.tasks.splice(i, 1)
    }
  }
}
</script>

<style scoped>
.btn-xl {
  padding: 5px 10px;
  font-size: 15px;
  border-radius: 5px;
  width: 500px;
}

.centerPos {
  margin-left: 200px !important;
}

@keyframes creation {
  0% {
    background-color: lightblue;
  }
  50% {
    background-color: lightblue;
  }
  100% {
    background-color: none;
  }
}

#listPart {
  animation-name: creation;
  animation-duration: 2s;
}
.card{
  width: 500px !important;
  margin-left:432px;
}
</style>