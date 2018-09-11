<template>
  <div>
      <br>
    <div class="text-center">
      <button class="btn btn-danger" @click="clear">Clean All</button>
      <input id="searchField" class="form-control" v-on:keyup="search" type="text" placeholder="Search...">
              <hr>
              <input id="taskName" class="form-control" type="text" placeholder="Name...">
              <textarea id="taskInput" class="form-control w-50 centerPos" v-bind:placeholder="info"></textarea>
              <hr>
              <button class="btn btn-secondary btn-xl" @click="createSome()">
              <i class="fa fa-plus"></i>
              </button>
              <br><br>
              <div class="list-group" v-if="view" v-for="(task,i) in tasks">
                <div id="listPart" class="card" v-if="task.displays">
                  <div class="card-header text-left">
                    <table>
                      <tr>
                        <td>
                     <span><kbd class="bg-info">{{ count(i) }}</kbd></span>
                     <span id="elemCounterIndex" style="visibility:hidden !important">{{ task.ind }}</span>
                     </td>
                     <td style="margin-left: 150px">
                       <span class="taskNameField">{{ task.name }}</span>
                       </td>
                     </tr>
                     </table>
                     <button class="btn btn-danger" @click="dropSome(task.ind)" style="margin-left:200px;">
                      <i class="fa fa-times"></i>
                    </button>
                  </div>
                  <div class="card-body">
                    <blockquote class="blockquote mb-0 text-left">
                      <p id="taskTextContent" class="taskTextField">{{task.text}}</p>
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
export default {
  data() {
    return {
      tasks: [
        {
          ind: 0,
          name: "",
          text: "",
          time: "",
          displays: false
        }
      ],
      view: true,
      err: false,
      info: "Task..."
    };
  },
  created() {
   var indexes = [] 
   for(var s=0; s<localStorage.length; s++){
     if(s!=0){
     indexes.push(JSON.parse(localStorage[s]).ind)
   }
 }
  var elems = []
  for(var i=1; i<localStorage.length; i++){
   for(var j=0; j<indexes.length; j++){
    if(JSON.parse(localStorage[i]).ind == indexes[j]){
     elems.push(JSON.parse(localStorage[i]))
    }
   }
 }
   this.tasks = elems
   this.view = true 
//tasks in memory and in tasks but not displaying
  },
  methods: {
    search() {
      // test for working, need styling or smthng
      var inpt = $("#searchField").val();
      var elems = document.getElementsByClassName("taskNameField")
      for (var i = 0; i < elems.length; i++) {
        if (inpt == elems[i].innerHTML) { 
          $(elems[i])
            .closest("#listPart")
            .addClass("visible");
        }
      }
      var texts = document.getElementsByClassName("taskTextField")
      for (var j = 0; j < texts.length; j++) {
        if (inpt == elems[j].innerHTML) { 
          $(elems[i])
            .closest("#listPart")
            .addClass("visible");
        }
      }
    },
    count(i) {
      i++;
      return i;
    },
    warning(message = "", inp = null) {
      inp.style.cssText = "border:2px solid red";
      this.info = message;
      setTimeout(
        function() {
          this.info = "Task...";
          inp.style.cssText = "border:1px solid grey";
        }.bind(this),
        2500
      );
    },
    rememberTask(Tname = "", Ttext = "", Ttime = "", Tdisplays = true) {
      var Tind = 1;
      if (this.tasks.length > 0) {
        for (var i = 0; i < this.tasks.length; i++) {
          if (this.tasks[i] == this.tasks[this.tasks.length - 1]) {
            Tind += this.tasks[i].ind;
          }
        }
      }
      if ((Tname == "") && (Ttext == "") && (Ttime == "")) {
        Tdisplays = false;
      }
      var objSet = {
        ind: Tind,
        name: Tname,
        text: Ttext,
        time: Ttime,
        displays: Tdisplays
      }
      this.tasks.push(objSet)
      localStorage.setItem(Tind, JSON.stringify(objSet));
      this.$noty.success("Task added successfully!");
    },
    timeFormat() {
      return new Date().toLocaleString();
    },
    isOldTask(inp, name_inp) {
      for (var i = 0; i < this.tasks.length; i++) {
        if ((this.tasks[i].text == inp.value) || (this.tasks[i].name == name_inp.value)) {
          this.err = true;
          inp.value = "";
          this.warning("This task already exist", inp);
        }
      }
    },
    createSome() {  
      var inp = document.getElementById("taskInput")
      var name_inp = document.getElementById("taskName")
      if ((inp.value == "") && (name_inp.value == "")) {
        this.warning("Nothing to record", inp)
      } else {
        this.isOldTask(inp, name_inp)
        if (!this.err) {
          this.rememberTask(name_inp.value, inp.value, this.timeFormat())
          inp.value = ""
          name_inp.value = ""
        }
      }
      this.err = false;
    },
    dropSome(i) {
         for(var j = 0; j<this.tasks.length; j++){
           if(this.tasks[j].ind == i){
            //localStorage.removeItem(this.tasks[j].ind)
            //this.tasks.splice(this.tasks[j], 1)
           alert(this.tasks[j])
           }
         }
      this.$noty.success("Task has been removed!");
    },
    clear() {
      // deletion must be in time
      var check;
      if (this.tasks.length > 0) {
        check = true;
      } else {
        check = false;
      }
      var tasksList = document.getElementsByClassName("list-group");
      for (var i = 0; i < tasksList.length; i++) {
        tasksList[i].setAttribute("id", "delete");
      }
      var self = this;
      setTimeout(function() {
        self.tasks = [];
        localStorage.clear();
      }, 1500);
      if (check) {
        this.$noty.success("All tasks removed successfully!");
      } else {
        this.$noty.warning("Nothing to delete");
      }
    }
  }
};
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
.card {
  width: 500px !important;
  margin-left: 432px;
}

.delete {
  opacity: 0;
  transition: 0.5s;
}

#delete {
  margin-left: 250px !important;
  opacity: 0;
  transition: 0.5s;
}

.hide {
  visibility: hidden !important;
}
.visible {
  border: 5px solid lightblue !important;
}
</style>




