<template>
  <div>
      <br>
    <div class="text-center">
      <button class="btn btn-danger" @click="clear">Clean All</button>
              <input id="taskName" class="form-control w-50 centerPos" type="text" placeholder="Name...">
              <textarea id="taskInput" class="form-control w-50 centerPos" v-bind:placeholder="info"></textarea>
              <hr>
              <button class="btn btn-secondary btn-xl" @click="createSome">
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
                     <span id="elemCounterIndex">{{ task.ind }}</span>
                     </td>
                     <td style="margin-left: 150px">
                       <span class="taskNameField">{{ task.name }}</span>
                      </td>
                     </tr>
                     </table>
                    <button class="btn btn-success" data-toggle="modal" :data-target="'#demo' + task.ind" style="margin-left:250px;">
                      <i class="fa fa-edit"></i>
                    </button>
<div class="modal fade" :id="'demo' + task.ind" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel"><i class="fa fa-edit"></i> Edit task</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <input id="taskName" class="form-control" type="text" :value="task.name" v-on:keyup="task.name = $event.target.value" placeholder="Name...">
        <br>
        <textarea id="taskInput" class="form-control w-50 centerPos" :value="task.text" v-on:keyup="task.text = $event.target.value" v-bind:placeholder="info"></textarea>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" @click="edit(task.ind, task.name, task.text)" data-dismiss="modal">Ok</button>
      </div>
    </div>
  </div>
</div>
                     <button class="btn btn-danger" @click="dropSome(task.ind); task.displays = false;" style="margin-left:200px;">
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
    var indexes = [];
      for (var s = 0; s < localStorage.length; s++) {
        if (typeof localStorage[s] != 'undefined') {
          indexes.push(JSON.parse(localStorage[s]).ind);
        }
      }
    var elems = [];
    for (var i = 0; i < localStorage.length; i++) {
      for (var j = 0; j < indexes.length; j++) {
        if (typeof localStorage[i] != 'undefined') {
          if (JSON.parse(localStorage[i]).ind == indexes[j]) {
            elems.push(JSON.parse(localStorage[i]));
          }
        }
      }
    }
    this.tasks = elems;
    this.view = true;
    this.fresh();
  },
  methods: {
    edit (ind,name,text) {
         var local = JSON.parse(localStorage.getItem(ind));
         local.name = name;
         local.text = text;
         localStorage.removeItem(ind);
         localStorage.setItem(ind, JSON.stringify(local));
    },
    fresh() {
      if (localStorage.getItem("fresh")) {
        localStorage.removeItem("fresh");
      } else {
        localStorage.setItem("fresh", "1");
        location.reload();
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
      if (Tname == "" && Ttext == "" && Ttime == "") {
        Tdisplays = false;
      }
      var objSave = {
        ind: Tind,
        name: Tname,
        text: Ttext,
        time: Ttime,
        displays: Tdisplays
      };
      this.tasks.push(objSave);
      localStorage.setItem(Tind, JSON.stringify(objSave));
      this.$noty.success("Task added successfully!");
    },
    timeFormat() {
      return new Date().toLocaleString();
    },
    isOldTask(inp, name_inp) {
      for (var i = 0; i < this.tasks.length; i++) {
        if (
          this.tasks[i].text == inp.value ||
          this.tasks[i].name == name_inp.value
        ) {
          this.err = true;
          inp.value = "";
          name_inp.value = "";
          this.warning("", name_inp);
          this.warning("This task already exist", inp);
        }
      }
    },
    createSome() {
      var inp = document.getElementById("taskInput");
      var name_inp = document.getElementById("taskName");
      if (inp.value == "" && name_inp.value == "") {
        this.warning("Nothing to record", inp);
      } else {
        this.isOldTask(inp, name_inp);
        if (!this.err) {
          this.rememberTask(name_inp.value, inp.value, this.timeFormat());
          inp.value = "";
          name_inp.value = "";
        }
      }
      this.err = false;
    },
    dropSome(task_ind) {
      localStorage.removeItem(task_ind);
      this.$noty.success("Task has been removed!");
      this.fresh();
      throw true;
    },
    clear() {
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
      }, 500);
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
#elemCounterIndex {
  visibility: hidden !important;
}
</style>

