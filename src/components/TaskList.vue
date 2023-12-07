<script>

export default {
  data() {
    return {
      titulo: '',
      descripcion: '',
      errors: [],
      editingTask: null,
      tasks: JSON.parse(localStorage.getItem('tasks')) || [],
      titleUpdate: '',
      descriptionUpdate: '',

    }

  },
  methods: {
    validate() {
      this.errors = [];

      if (this.titulo === '') {
        this.errors.push("Introduzca un nombre para la tarea.");
      }
      if (this.descripcion === '') {
        this.errors.push('Introduzca una descripción para la tarea.');
      }
      if (this.errors.length > 0) {
        return false;
      } else {
        this.addTask()
      }

    },

    addTask() {
        let task = {
          title: this.titulo,
          description: this.descripcion,
          completed: false
        }
        this.tasks.push(task)
        this.titulo = ''
        this.descripcion = ''
        this.errors = []
    },

    editTask(index) {
      if (this.editingTask == index) {
        this.editingTask = null;
      }else {
        this.titleUpdate = this.tasks[index].title;
        this.descriptionUpdate = this.tasks[index].description;
        this.editingTask = index
      }

      return;
    },

    updateTask(index) {

      this.errors = [];

      if (this.titleUpdate === '') {
        this.errors.push("Introduzca un nombre para la tarea.");
      }
      if (this.descriptionUpdate === '') {
        this.errors.push('Introduzca una descripción para la tarea.');
      }
      if (this.errors.length > 0) {
        return false;
      }

      this.tasks[index].title = this.titleUpdate
      this.tasks[index].description = this.descriptionUpdate
      this.editingTask = null

    },

    checkEdit(index) {
      if(this.editingTask == index) {
        return 'text';
      }else {
        return 'hidden';
      }
    },

    deleteTask(index) {
      this.tasks.splice (index, 1)
      return;
    },

    completeTask(index) {
      this.tasks[index].completed = !this.tasks[index].completed
      
    }
  },
  watch: {
    tasks: {
      handler() {
        localStorage.setItem('tasks', JSON.stringify(this.tasks))
      },
      deep: true
    }

  },
  computed : {
    totalTasks() {
      return this.tasks.length
    },
    totalCompleted() {
      return this.tasks.filter(task => task.completed === true ).length
    },
    totalPending () {
      return this.tasks.filter(task => task.completed === false ).length
    },
    percentageComplete () {
      return this.tasks.length > 0 ? (this.totalCompleted / this.tasks.length) * 100 : 0
    }
  }
};

</script>

<template>

    <div class="input-group mb-3">
      <label class="input-group-text" for="inputGroupSelect01">Título</label>
      <input v-model="titulo" type="text" class="form-control" placeholder="">
    </div>
    <div class="input-group mb-3">
      <label class="input-group-text" for="inputGroupSelect02">Descripción</label>
      <input v-model="descripcion" type="text" class="form-control" placeholder="">
    </div>
    <div class="input-group mb-3">
      <button type="button" class="btn btn-primary" @click="validate">Agregar tarea</button>    
    </div>

    <p v-if="errors.length">
      <b>Error:</b>
      <ul>
        <li v-for="error in errors">{{ error }}</li>
      </ul>
    </p>
    


    <div>
      <div class="d-flex align-items-center justify-content-center gap-2">
        <span class="fs-5"> Completadas <span class="fs-1"> {{ this.totalCompleted }}</span></span>
        <span class="fs-1"> / </span>
        <span class="fs-5"> <span class="fs-1"> {{ this.totalTasks }}</span> Todos</span>
    </div>

    <div class="progress mb-3" role="progressbar" aria-label="Example with label" aria-valuenow="25" aria-valuemin="0" aria-valuemax="100">
        <div class="progress-bar" :style="{ width: percentageComplete + '%'}">{{percentageComplete}}%</div>
    </div>

    <div class="list-group">
      <div v-for="(task, index) in tasks" :key="index" class="list-group-item list-group-item-action" aria-current="true" 
      :style ="{'background-color': task.completed ? '#0080002e' : 'initial'}"
      >
        <div class="d-flex w-100 flex-column">
          <h5 class="mb-1" 
          :style ="{'text-decoration': task.completed ? 'line-through' : 'none', 'display' : editingTask == index ? 'none' : 'block'}"
          
          
          >{{ task.title }}</h5>

          <p class="mb-3" 
          :style ="{'text-decoration': task.completed ? 'line-through' : 'none', 'display' : editingTask == index ? 'none' : 'block'}"
          >{{ task.description }}</p>

        <form @submit.prevent="updateTask(index)" class="mb-3"
        :style ="{'display' : editingTask == index ? 'block' : 'none'}"
        >
          <input :type="checkEdit(index)" class="form-control mb-2" name="title" v-model="titleUpdate">
          <input :type="checkEdit(index)" class="form-control mb-2" name="description" v-model="descriptionUpdate">
          <input type="hidden" class="form-control mb-2" v-model="task.description" name="index" value="{{  index  }}">
          <button type="submit" class="btn btn-primary">Actualizar</button>
        </form>

      </div>
        <div class="btn-group gap-2">
          <button v-if="!task.completed" @click="completeTask(index)" type="button" class="btn btn-success"><i class="bi bi-bookmark-check">Marcar como completa</i></button>
          <button v-if="task.completed" @click="completeTask(index)" type="button" class="btn btn-warning"><i class="bi bi-bookmark">Marcar como pendiente</i></button>
          <button @click="editTask(index)" type="button" class="btn btn-primary"><i class="bi bi-pen">Editar tarea</i></button>
          <button @click="deleteTask(index)" type="button" class="btn btn-danger"><i class="bi bi-x-circle">Borrar tarea</i></button>
        </div>

      </div>
    </div>


  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
