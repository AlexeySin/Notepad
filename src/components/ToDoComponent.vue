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
              <div class="list-group" v-if="view" v-for="task in tasks">
                <div class="card" v-if="task.displays" id="listPart">
    
                  <div class="card-header text-left">
                     {{ task.counter }}
                     <button class="btn btn-danger" @click="dropSome(task.counter)" style="margin-left:200px;">
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
        counter: 1,
        text: '',
        time: '',
        displays: false
      }],
      view: false,
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
      isOldTask(inp,count){
        for (var i = 0; i < this.tasks.length; i++) {
            if (this.tasks[i].text == inp.value) {
              alert('smae')
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
// Ты отображашь тасочки из памяти и вообщем только при создании новой
// Нужно их отображать изначально.
var last = this.tasks[this.tasks.length-1]
if(!last){
 last = 1
}else{
  alert('Yahoo')
  last = this.tasks[this.tasks.length-1].counter++
}
alert(last)

        var inp = document.getElementById('taskInput')
        if (inp.value == '') {
          alert('Nothing to record!')
        } else {
          this.view = true

// Тут 2 аргумент никчему
         // this.isOldTask(inp,taskCounter)
        
          if (this.err == false) {

            this.tasks.push({
              counter: last,
              text: inp.value,
              time: new Date(),
              displays: true
            })
// Время отображается криво
            inp.value = ''
            localStorage.setItem("tasks", JSON.stringify(this.tasks))

            alert('Task index: ' + this.counter)
          }
        }
        this.err = false
      },
      dropSome (i) {
        this.tasks.counter--
        localStorage.removeItem(localStorage.getItem(i));
        this.tasks.splice(i, 1)
      },
      clear () {
        this.counter = 0
        this.tasks = []
        localStorage.clear()
      }
      //нужен транизшен на удаление
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