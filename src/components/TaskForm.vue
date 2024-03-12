<template>
    <div class="task-form">
        <input type="text" class="task-input" v-model="text" placeholder="Enter task..." />
        <button class="add-button" @click="addTask">Add Task</button>
    </div>
</template>

<script>
export default {
    name: 'TaskForm',
    data() {
        return {
            text: ''
        };
    },
    methods: {
        addTask() {
            if (this.text.trim() === '') return;
            this.$emit('addTask', {
                text: this.text,
                completed: false,
                createdAt: new Date()
            });
            this.text = '';
        }
    }
};
</script>

<style>
.task-form {
    margin-bottom: 20px;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
}

.task-input {
    flex: 1;
    width: 200px;
    padding: 8px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 5px;
    outline: none;
    margin-right: 10px;
}

.add-button {
    padding: 8px 16px;
    font-size: 16px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    background-color: #007bff;
    color: #fff;
}

.add-button:hover {
    background-color: #0056b3;
}

@media screen and (max-width: 600px) {
    .task-input {
        width: 100%;
        margin-right: 0;
        margin-bottom: 10px;
    }
}
</style>
