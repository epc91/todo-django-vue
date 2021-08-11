<template>
  <div class="home">
    <h1 class="title">Task App</h1>
    <hr>
    <!-- Column #1 -->
    <div class="columns">
      <!-- Sub Column  #1 -->
      <div class="column is-3 is-offset-3">
        <form v-on:submit.prevent="addTask">
          <h2 class="subtitle">Add Task</h2>
          <!-- Field #1 -->
          <div class="field">
            <label class="label">Description</label>
            <div class="control">
              <input type="text" class="input" v-model="description">
            </div>
          </div>
          <!-- Field #2 -->
          <div class="field">
            <label class="label">Status</label>
            <div class="control">
              <div class="select">
                <select v-model="status">
                  <option value="todo">To Do</option>
                  <option value="done">Done</option>
                </select>
              </div>
            </div>
          </div>
          <!-- Field #3 -->
          <div class="field">
            <div class="control">
              <button class="button is-link">Submit</button>
            </div>
          </div>
        </form>
      </div>
    </div>
    <!-- Column #2 -->
    <div class="columns">
      <!-- Sub Column #1 -->
      <div class="column is-6">
        <h2 class="subtitle">To Do</h2>
        <div class="todo">
          <div class="card" v-for="task in tasks" v-if="task.status === 'todo'" v-bind:key="task.id" >
            <div class="card-content">{{ task.description }}</div>
            <footer class="card-footer">
              <a class="card-footer-item" v-on:click="setStatus(task.id, 'done')">Done</a>
            </footer>
          </div>
        </div>
      </div>
      <!-- Sub Column #2 -->
      <div class="column is-6">
        <h2 class="subtitle">Done</h2>
        <div class="done">
          <div class="card" v-for="task in tasks" v-if="task.status === 'done'" v-bind:key="task.id" >
            <div class="card-content">{{ task.description }}</div>
            <footer class="card-content">
              <a class="card-footer-item" v-on:click="setStatus(task.id, 'todo')" >To do</a>
            </footer>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data () {
    return {
      tasks: [],
      description: '',
      status: 'todo'
    }
  },
  mounted () {
    this.getTasks()
  },
  methods: {
    // GET
    getTasks() {
      axios({
        method: 'get',
        url: 'http://127.0.0.1:8000/tasks/',
        auth: {
          username: 'epc91',
          password: 'qwerty'
        }
      }).then(response => this.tasks = response.data)
    },
    // POST
    addTask() {
      if (this.description) {
        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/tasks/',
          data: {
            description: this.description,
            status: this.status,
          },
          auth: {
            username: 'epc91',
            password: 'qwerty',
          }
        })
        .then(response => {
          let newTask = {
            'id': response.data.id,
            'description': this.description,
            'status': this.status
          }
          this.tasks.push(newTask),
          this.description = '',
          this.status = 'todo'
        })
        .catch(error => {
          console.log(error);
        })
      }
    },
    // PUT
    setStatus(task_id, status) {
      const task = this.tasks.filter(task => task.id === task_id)[0]
      const description = task.description
      axios({
        method: 'put',
        url: 'http://127.0.0.1:8000/tasks/' + task_id + '/',
        headers: {
          'Content-Type': 'application/json',

        },
        data: {
          description: description,
          status: status,
        },
        auth: {
          username: 'epc91',
          password: 'qwerty',
        },

      })
      .then(() => {
        task.status = status
      })
    }

  }

}
</script>

<style lang="scss">
.select, select {
  width: 100%;
}

.card {
  margin-bottom: 20px;
}

.done {
  opacity: 0.3;
}
</style>
