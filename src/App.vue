<template>
  <div class="container">
    <div class="row">
      <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
        <h1>To Do list</h1>
        <button class="btn btn-primary" id="user_id_btn" @click="submit">Get user id</button>
        <hr>
        <p>User id : {{ user_id }}</p>
        <br><br>
        <div class="form-group">
          <label>Task Name</label>
          <input type="text" class="form-control" v-model="taskname">
        </div>
        <button class="btn btn-primary" id="btn_add_todo" disabled='true' @click="sendToDo">Add To Do</button>
        <br><br>
        <ul class="list-group">
          <li class="list-group-item" v-for="t in tasks" :key="t.item" :id=t.id>
              <button type="button" class="btn btn-danger btn-sm" @click="deleteTask">delete</button>
              <input type="text" class="form-control" :placeholder=t.name>
              <button type="button" class="btn btn-info btn-sm" @click="modifyTask">Modify</button>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      user_id: '',
      taskname: '',
      tasks: []
    }
  },
  methods: {
    submit() {
      this.$http.post("https://glo3102lab4.herokuapp.com/users")
              .then(response => {
                return response.json();
              }, error => {
                // eslint-disable-next-line no-console
                console.log(error);
                // eslint-disable-next-line no-console
              }).then(data => {
        this.user_id = data['id'];
        document.getElementById('user_id_btn').disabled = true;
        document.getElementById('btn_add_todo').disabled = false;
      })
    },
    sendToDo () {
      if (this.taskname === '') {
        alert("Le champ Task Name ... ne doit pas être vide");
        return;
      }
      this.$http.post('https://glo3102lab4.herokuapp.com/' + this.user_id + '/tasks', {"name": this.taskname})
              .then(response => {
                return response.json();
              }).then(() => {

        // va lire l'ensemble des tâches du user
        this.$http.get("https://glo3102lab4.herokuapp.com/" + this.user_id + "/tasks")
                .then(response => {
                  return response.json();
                }).then(data => {
                  this.tasks = [];
                  data.tasks.forEach(element => {
                  this.tasks.push({'id':element.id, 'name':element.name});
                  });
                });
              })
    },

    deleteTask (event) {
      let task_id = event.target.parentNode.id;
      // supprime la tâche sur le serveur
      this.$http.delete('https://glo3102lab4.herokuapp.com/' + this.user_id + '/tasks/' + task_id);
      // supprime la tâche dans le tableau
      let index_found = this.tasks.findIndex((element) => element.id === task_id);
      this.tasks.splice(index_found, 1);
    },

    modifyTask (event) {
        // récupère le texte du input associé au bon <li>
        let task_id = event.target.parentNode.id;
        let my_li = document.getElementById(task_id);
        let new_text = my_li.getElementsByTagName('input')[0].value;
        if(new_text==""){
            // si c'est vide fin de l'opération
            alert("Le champ Todo Modif... ne doit pas être vide");
            return;
        }
        my_li.getElementsByTagName('input')[0].value = '';
        my_li.getElementsByTagName('input')[0].placeholder = new_text;
        // met à jour le serveur
        this.$http.put('https://glo3102lab4.herokuapp.com/' + this.user_id + '/tasks/' + task_id, {"name": new_text});
        // met à jour le tableau
        let index_found = this.tasks.findIndex((element) => element.id === task_id);
        this.tasks[index_found].name = new_text;
    },
  }
}



</script>

<style>
</style>
