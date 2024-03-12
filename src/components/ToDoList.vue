<template>
    <div class="todo-list">
        <h1>To-Do List</h1>
        <TaskForm @addTask="addTask" />
        <h2>Open Tasks</h2>
        <div v-if="openTasks.length === 0" class="no-tasks">No open tasks</div>
        <div v-else>
            <div v-for="task in openTasks" :key="task.id">
                <TaskItem :task="task" @updateTask="updateTask" @deleteTask="deleteTask"
                    @toggleComplete="toggleComplete" />
            </div>
        </div>
        <h2>Completed Tasks</h2>
        <div v-if="completedTasks.length === 0" class="no-tasks">No completed tasks</div>
        <div v-else>
            <div v-for="task in completedTasks" :key="task.id">
                <TaskItem :task="task" @updateTask="updateTask" @deleteTask="deleteTask"
                    @toggleComplete="toggleComplete" />
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import TaskItem from './TaskItem.vue';
import TaskForm from './TaskForm.vue';

export default {
    name: 'ToDoList',
    components: {
        TaskItem,
        TaskForm
    },
    data() {
        return {
            tasks: []
        };
    },
    computed: {
        openTasks() {
            return this.tasks.filter(task => !task.completed);
        },
        completedTasks() {
            return this.tasks.filter(task => task.completed);
        }
    },
    mounted() {
        this.fetchTasks();
    },
    methods: {
        fetchTasks() {
            axios.get('http://localhost:3000/tasks')
                .then(response => {
                    this.tasks = response.data;
                })
                .catch(error => {
                    console.error('Error fetching tasks:', error);
                });
        },
        addTask(task) {
            axios.post('http://localhost:3000/tasks', task)
                .then(() => {
                    this.fetchTasks();
                })
                .catch(error => {
                    console.error('Error adding task:', error);
                });
        },
        updateTask(task) {
            axios.put(`http://localhost:3000/tasks/${task.id}`, task)
                .then(() => {
                    this.fetchTasks();
                })
                .catch(error => {
                    console.error('Error updating task:', error);
                });
        },
        deleteTask(id) {
            axios.delete(`http://localhost:3000/tasks/${id}`)
                .then(() => {
                    this.fetchTasks();
                })
                .catch(error => {
                    console.error('Error deleting task:', error);
                });
        },
        toggleComplete(task) {
            task.completed = !task.completed;
            this.updateTask(task);
        }
    }
};
</script>

<style>
.todo-list {
    display: flex;
    flex-direction: column;
    max-width: 100%;
    /* justify-content: center; */
    align-items: center;
    margin: 0 auto;
    background-color: #9a9696;
    height:auto;
    min-height: 97.5vh;
    border-radius: 5px;
}

h1, h2 {
    font-size: 2em;
    margin-bottom: 10px;
}

.no-tasks {
    font-style: italic;
    color: #666;
    margin-bottom: 2%;
}

.no-tasks::before {
    content: "-";
    margin-right: 5px;
    color: #999;
}

.no-tasks::after {
    content: "-";
    margin-left: 5px;
    color: #999;
}

@media screen and (max-width: 768px) {
    h1, h2 {
        font-size: 1.5em;
    }
}
</style>
