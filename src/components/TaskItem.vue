<template>
    <div class="task-item">
        <div class="task-content">
            <input type="checkbox" class="checkbox" v-model="isChecked" @change="toggleComplete" />
            <span v-if="!editMode" class="task-text">{{ task.text }}</span>
            <input v-else type="text" class="edit-input" v-model="editedText" @keydown.enter="saveTaskText"
                @blur="saveTaskText" />
            <!-- Conditionally render the edit button only for open tasks -->
            <button v-if="!isChecked && !editMode" class="edit-button" @click="toggleEditMode">Edit</button>
            <!-- Conditionally render the save button only for open tasks -->
            <button v-if="editMode && !isChecked" class="save-button" @click="saveTaskText">Save</button>
            <button class="delete-button" @click="deleteTask">Delete</button>
        </div>
    </div>
</template>

<script>
export default {
    name: 'TaskItem',
    props: ['task'],
    data() {
        return {
            isChecked: this.task.completed,
            editMode: false,
            editedText: this.task.text
        };
    },
    methods: {
        deleteTask() {
            this.$emit('deleteTask', this.task.id);
            this.saveTasksToLocalStorage();
        },
        toggleComplete() {
            this.isChecked = !this.isChecked;
            this.$emit('toggleComplete', this.task);
            this.saveTasksToLocalStorage();
        },
        toggleEditMode() {
            if (!this.editMode) {
                // Entering edit mode, save current text to editedText
                this.editedText = this.task.text;
            }
            this.editMode = !this.editMode;
        },
        saveTaskText() {
            if (this.editedText.trim() === '') {
                // Reset to original text if the edited text is empty
                this.editedText = this.task.text;
            } else {
                // Emit an event to update the task text
                const updatedTask = { ...this.task, text: this.editedText };
                if (this.task.completed) {
                    this.$emit('updateCompletedTask', updatedTask);
                } else {
                    this.$emit('updateTask', updatedTask);
                }
            }
            // Save tasks to local storage
            this.saveTasksToLocalStorage();
            // Exit edit mode
            this.editMode = false;
        },
        formatDate(dateString) {
            // Assuming dateString is in ISO format
            const date = new Date(dateString);
            return `${date.toLocaleDateString()} ${date.toLocaleTimeString()}`;
        },
        saveTasksToLocalStorage() {
            // Retrieve existing tasks from local storage
            const tasks = JSON.parse(localStorage.getItem('tasks')) || [];
            // Update tasks with current list of tasks
            const updatedTasks = tasks.map(task => {
                if (task.id === this.task.id) {
                    return this.task;
                }
                return task;
            });
            // Save updated tasks to local storage
            localStorage.setItem('tasks', JSON.stringify(updatedTasks));
        },
    },
    mounted() {
        // Retrieve tasks from local storage when the component is mounted
        const tasks = JSON.parse(localStorage.getItem('tasks')) || [];
        // Find the current task in the retrieved tasks list
        const foundTask = tasks.find(task => task.id === this.task.id);
        if (foundTask) {
            // Update task data with retrieved task data
            this.isChecked = foundTask.completed;
            this.editedText = foundTask.text;
        }
    }
};
</script>

<style>
.task-item {
    width: 30vw;
    height: auto;
    margin-bottom: 3%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    background-color: #f9f9f9;
    display: flex;
    justify-content: center;
    flex-direction: column; /* Change to column direction */
    align-items: column; /* Align items to start of column */
}

.task-content {
    display: flex;
    flex-direction: column; /* Change to column direction */
    align-items: center; /* Align items to center horizontally */
    
}

.created-date {
    font-size: 0.8em;
    color: #666;
    margin-bottom: 5%;
}

.checkbox {
    margin-bottom: 5px; /* Add margin between checkbox and text */
}

.task-text {
    font-size: 1em;
    margin-bottom: 10px; /* Add margin between text and buttons */
    word-wrap: break-word; 
    overflow-wrap: break-word;
    /* overflow:-moz-hidden-unscrollable;  */
    width: 30vw;
    border: 2px solid gray;
}

.edit-input {
    width: 100%; /* Take full width */
    font-size: 1em;
    margin-bottom: 10px; /* Add margin between input and buttons */
}

.edit-button,
.delete-button {
    padding: 5px 10px;
    /* margin-left: auto; Align buttons to right */
    margin-bottom: 2%;
    border: none;
    border-radius: 3px;
    cursor: pointer;
    background-color: #007bff;
    color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
}

.delete-button {
    background-color: #dc3545;
}

.edit-button:hover,
.delete-button:hover {
    background-color: #0056b3;
}

.save-button {
    padding: 5px 10px;
    margin-left: 5px;
    border: none;
    border-radius: 3px;
    cursor: pointer;
    background-color: #28a745;
    color: #fff;
    margin-bottom: 2%;
}

.save-button:hover {
    background-color: #218838;
}

@media screen and (max-width: 600px) {
    .task-text {
        width: 95%; /* Allow text to take full width */
    }

    .edit-input {
        width: 100%;
        
    }

    .checkbox {
        margin-bottom: 5px; /* Add margin between checkbox and text */
    }

    .edit-button,
    .delete-button {
        margin-left: 0; 
    }

    .task-item{
        align-items: center;
        justify-content: center;
        width: 80vw;
    }

    .task-content{
        width: 85%;
    }
}


</style>
