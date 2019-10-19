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
        <button class="btn btn-primary" @click="sendToDo">Add To Do</button>
        <ul class="list-group">
          <li class="list-group-item" v-for="t in tasks" :key="t.item" :id=t.id>
              <button type="button" class="btn btn-danger btn-sm">delete</button>
              <input type="text" class="form-control" :placeholder=t.name>
              <button type="button" class="btn btn-info btn-sm">Modify</button>
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
      })
    },
    sendToDo () {

      this.$http.post('https://glo3102lab4.herokuapp.com/' + this.user_id + '/tasks', {"name":this.taskname})
              .then(response => {
                return response.json();
              }).then( task => {
                this.tasks.push(task);
        // eslint-disable-next-line no-console
        console.log(this.tasks);
      })
    }
  }
}
</script>

<style>
</style>
