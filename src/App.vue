<template>
  <div id="app">
    <header class="navbar">
      <section class="navbar-section"></section>
      <section class="navbar-section">
        <div class="input-group input-inline">
          <button class="bcb-1 btn btn-action s-circle backgroundColorButtons" @click="changeBackgroundColor ($event)"></button>
          <button class="bcb-2 btn btn-action s-circle backgroundColorButtons" @click="changeBackgroundColor ($event)"></button>
          <button class="bcb-3 btn btn-action s-circle backgroundColorButtons" @click="changeBackgroundColor ($event)"></button>
        </div>
      </section>
    </header>

    <div class="container grid-sm">
      <div class="columns">
        <div class="column logo-todo">
          <div class="text-primary"><h1><span class="text-muted">Todo</span><span class="text-bold">Vue</span></h1></div>
        </div>
      </div>

      <div class="input-group add-task-div">
        <input type="text" class="form-input" v-model="todo.description" placeholder="Adicionar uma tarefa.">
        <button class="btn btn-primary input-group-btn" @click="modalOpeningProcesses">Adicionar</button>
      </div>

      <table class="table table-striped table-hover tasks-table">
        <thead class="text-gray">
          <tr>
            <th>N°</th>
            <th>Tarefa</th>
            <th>Prioridade</th>
            <th>Status</th>
            <th>Ação</th>
          </tr>
        </thead>
        <tbody class="bg-secondary">
          <tr v-for="(v, n) in todos" v-bind:key="v.id">
            <td>{{ n }}</td>
            <td>{{ v.task }}</td>
            <td>{{ v.priority }}</td>
            <td><button class="btn btn-sm" @click="changeStatus(v.id)">{{ v.taskStatus }}</button></td>
            <td><button class="btn btn-sm s-circle" @click="openModal(); openEditTask(v)"><i class="icon icon-1x icon-edit"></i></button>  <button class="btn btn-sm  s-circle" @click="deleteTask(v.id)"><i class="icon icon-1x icon-delete"></i></button> </td>
          </tr>
        </tbody>
      </table>
    </div>

    <router-view />

    <div class="modal modal-sm" :class="{active: isActive}">
      <div class="modal-container">
        <div class="modal-header">
          <a class="btn btn-clear float-right" aria-label="Close" @click="closeModal"></a>
          <div class="modal-title h5">Adicionar Tarefa</div>
        </div>
        <div class="modal-body">
          <div class="content">
            <form @submit.prevent class="insertion-form">
              <div class="form-group">
                <label class="form-label" for="input-example-1">Tarefa</label>
                <input class="form-input" type="text" v-model="todo.task">
              </div>

              <div class="form-group">
                <label class="form-label" for="input-example-1">Prioridade</label>

                <label class="form-radio form-inline">
                  <input v-model="todo.priority" type="radio" name="priority" v-bind:value="'alta'" checked>
                  <i class="form-icon"></i> Alta
                </label>
                <label class="form-radio form-inline">
                  <input v-model="todo.priority" type="radio" name="priority" v-bind:value="'media'">
                  <i class="form-icon"></i> Média
                </label>
                <label class="form-radio form-inline">
                  <input v-model="todo.priority" type="radio" name="priority" v-bind:value="'baixa'">
                  <i class="form-icon"></i> Baixa
                </label>

              </div>
            </form>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-primary input-group-btn" @click="addOrEdit === 0 ? addTodo() : editTask(todo)">Finalizar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      todos: [],
      todo: {priority: 'alta'},
      isActive: false,
      addOrEdit: 0
    }
  },
  mounted () {
    document.body.style.backgroundImage = 'linear-gradient(to right, #545556, #093040)'
  },
  methods: {
    modalOpeningProcesses () {
      if (this.todo.description && this.todo.description !== '') {
        this.isActive = !this.isActive
        this.todo.task = this.todo.description
        this.todo.id = Date.now()
        this.todo.taskStatus = 'Pendência'
        this.todo.description = ''
        delete this.todo.description
      }
    },
    openModal () {
      this.isActive = true
    },
    closeModal () {
      this.isActive = false
      this.todo = {priority: 'alta'}
      this.addOrEdit = 0
    },
    addTodo () {
      this.todos.push(this.todo)
      this.todo = {priority: 'alta'}
      this.isActive = false
    },
    changeStatus (id) {
      for (let x = 0; x < this.todos.length; x++) {
        if (this.todos[x].id === id) {
          if (this.todos[x].taskStatus === 'Pendência') {
            this.todos[x].taskStatus = 'Em andamento'
          } else if (this.todos[x].taskStatus === 'Em andamento') {
            this.todos[x].taskStatus = 'Feito'
          } else if (this.todos[x].taskStatus === 'Feito') {
            this.todos[x].taskStatus = 'Pendência'
          }
        }
      }
      this.todos = this.forceUpdate(this.todos)
    },
    // Essa função apenas prepara o Modal para posterior edição!
    openEditTask (todo) {
      this.addOrEdit = 1

      this.todo.id = todo.id
      this.todo.priority = todo.priority
      this.todo.task = todo.task
      this.todo.taskStatus = todo.taskStatus
    },
    // Essa função é quem de fato vai editar, diferente da de cima!
    editTask (todo) {
      for (let x = 0; x < this.todos.length; x++) {
        if (this.todos[x].id === todo.id) {
          this.todos[x] = todo
        }
      }

      this.todos = this.forceUpdate(this.todos)
      this.closeModal()
    },
    deleteTask (id) {
      for (let x = 0; x < this.todos.length; x++) {
        if (this.todos[x].id === id) {
          delete this.todos[x]
        }
      }
      this.todos = this.forceUpdate(this.todos)
    },
    // Forçar a atualização de alguma variável que não queira atualizar no vue
    forceUpdate (array) {
      array = array.filter((x) => {
        return x
      })
      return array
    },
    // Alternancia de cor de fundo
    changeBackgroundColor (element) {
      let btn = element.target.classList[0]
      let body = document.body
      if (btn === 'bcb-1') {
        body.style.backgroundImage = 'linear-gradient(to right, #545556, #093040)'
      } else if (btn === 'bcb-2') {
        body.removeAttribute('style')
        body.style.backgroundColor = '#fff'
      } else if (btn === 'bcb-3') {
        body.removeAttribute('style')
        body.style.backgroundColor = '#303742'
      }
    }
  }
}
</script>

<style>
.navbar {
  margin-top: 10px;
  margin-right: 10px;
}
.backgroundColorButtons {
  margin: 1px;
}
.bcb-1 { background-image: linear-gradient(to right, #545556, #093040); } .bcb-2 {  } .bcb-3 { background-color: #001a21; }
.container {
  margin-top: 40px;
}
.logo-todo {
  text-align: center;
}
.logo-todo img {
  width: 150px;
}
.add-task-div {
  margin-top: 40px;
}
.add-task-div input {
  border-radius: 25px;
}
.add-task-div button {
  border-radius: 25px;
}
.tasks-table {
  text-align: center;
  margin-top: 40px;
}
</style>
