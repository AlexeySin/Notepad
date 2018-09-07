<template>
  <div>
      <br>
    <div class="text-center">
      <button class="btn btn-danger" @click="clear">Clean All</button>
              <input class="form-control w-50 centerPos" type="text" v-bind:placeholder="info" id="taskInput">
              <hr>
              <button class="btn btn-secondary btn-xl" @click="createSome()">
              <i class="fa fa-plus"></i>
              </button>
              <br><br>
              <div class="list-group" v-if="view" v-for="(task,i) in tasks">
                <div class="card" v-if="task.displays" id="listPart">
                  <div class="card-header text-left">
                     <span v-if="i!=0">{{ i }}</span>
                     <button class="btn btn-danger" @click="dropSome(i)" style="margin-left:200px;">
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
              <hr>
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
      view: true,
      err: false,
      info: 'Task...',
      }
    },
    created(){
     if(localStorage.getItem("tasks") && localStorage.getItem("tasks").length>0){
     this.tasks = JSON.parse(localStorage.getItem("tasks"))
     }
    },
    methods: {
      rememberTask (Ttext = '',Ttime = '',Tdisplays = true) {
          if(Ttext == '' && Ttime == ''){
             Tdisplays = false
          }
            this.tasks.push({
              text: Ttext,
              time: Ttime,
              displays: Tdisplays
            })
      },
      timeFormat(){
         return new Date().toLocaleString()
      },
      isOldTask (inp) {
        for (var i = 0; i < this.tasks.length; i++) {
            if (this.tasks[i].text == inp.value) {
              alert('same')
              this.err = true
              inp.value = ''
  
              inp.style.cssText = "border:2px solid red"
              this.info = 'This task already created'
  
              setTimeout(function() {
                this.info = 'Task...'
                inp.style.cssText = "border:1px solid grey"
              }.bind(this), 2500)
              throw true
            }
          }
      },
      createSome () {
        var inp = document.getElementById('taskInput')
        if (inp.value == '') {
          alert('Nothing to record!')
          throw true;
        } else {
          this.isOldTask(inp)
          if (!this.err) {
          this.rememberTask(inp.value,this.timeFormat())
          inp.value = ''
          localStorage.setItem("tasks", JSON.stringify(this.tasks))
          }
        }
        this.err = false
      },
      dropSome (i) {
        //задавай єлементу с id = listPart 
        //новый айди вместо старого = id, в котором будет запускать анимацию удаления
        localStorage.removeItem(localStorage.getItem(i));
        this.tasks.splice(i, 1)
      },
      clear () {
        //animate here
        this.tasks = []
        localStorage.clear()
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
    margin-left: 100px !important;
  }
  
  @keyframes creation {
    0% {
      opacity: 0;
      margin-left: 50px;
      background-color: lightblue;
    }
    50% {
      opacity: 5;
      background-color: lightblue;
    }
    100% {
      opacity: 1;
      background-color: none;
    }
  }
  
  #listPart {
    animation-name: creation;
    animation-duration: 2.5s;
  }
  .card{
    width: 500px !important;
    margin-left:432px;
  }
  </style>