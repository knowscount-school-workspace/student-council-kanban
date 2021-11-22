<template>
  <div id="app">
    <div class="flex justify-center">
      <div class="min-h-screen flex overflow-x-scroll py-12">
        <div
          v-for="column in columns"
          :key="column.title"
          class="bg-gray-100 rounded-lg px-3 py-3 column-width rounded mr-4"
        >
          <p class="text-gray-700 font-semibold font-sans tracking-wide text-sm">{{column.title}}</p>
          <!-- Draggable component comes from vuedraggable. It provides drag & drop functionality -->
          <draggable :list="column.tasks" :animation="200" ghost-class="ghost-card" group="tasks">
            <!-- Each element from here will be draggable and animated. Note :key is very important here to be unique both for draggable and animations to be smooth & consistent. -->
            <task-card
              v-for="(task) in column.tasks"
              :key="task.id"
              :task="task"
              class="mt-3 cursor-move"
            ></task-card>
            <!-- </transition-group> -->
          </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
import axios from 'axios';
import TaskCard from "./components/TaskCard.vue";
export default {
  name: "App",
  components: {
    TaskCard,
    draggable
  },
  data() {
    return {
      columns: [],
    };
  },
  mounted () {
    let urlOne = 'http://localhost:3001/backlog'
    let urlTwo = 'http://localhost:3001/inprogress'
    let urlThree = 'http://localhost:3001/review'
    let urlFour = 'http://localhost:3001/done'
    const requestOne = axios.get(urlOne)
    const requestTwo = axios.get(urlTwo)
    const requestThree = axios.get(urlThree)
    const requestFour = axios.get(urlFour)
    // axios.get('http://localhost:3001/backlog').then(response => (this.columns = response))
    axios.all([requestOne, requestTwo, requestThree, requestFour]).then(axios.spread((...responses) => {
      this.columns = [
        {
          title: "Backlog",
          tasks: responses[0].data
        },
        {
          title: "In Progress",
          tasks: responses[1].data
        },
        {
          title: "Review",
          tasks: responses[2].data
        },
        {
          title: "Done",
          tasks: responses[3].data
        }
      ];
    }));
  }
};
</script>

<style scoped>
.column-width {
  min-width: 320px;
  width: 320px;
}
/* Unfortunately @apply cannot be setup in codesandbox, 
but you'd use "@apply border opacity-50 border-blue-500 bg-gray-200" here */
.ghost-card {
  opacity: 0.5;
  background: #F7FAFC;
  border: 1px solid #4299e1;
}
</style>
