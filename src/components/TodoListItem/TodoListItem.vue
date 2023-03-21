<template>
    <div>
        <div v-if="!editMode">
        <input type="checkbox" v-model="isChecked" />
        <span>
            <strong>{{todo.title}}</strong>
        </span>
        <span style="margin: 0 20px">
            {{todo.description}}
        </span>
<button @click="toggleEditTodo">Edit</button>
<button @click="removeTodo">Remove</button>
    </div>
    <div v-else>
<TodoForm :todo="todo"
isEditMode
@onUpdateTodo="handleUpdateTodo"
/>
            </div>  
        </div>

</template>

<script>
import TodoForm from '../TodoForm/TodoForm.vue'

export default {
    name: "TodoListItem",
    props: {
        todo: {
            type: Object,
            required: true
        }
    },
    computed: {
        isChecked: {
            get() {
                return this.todo.isChecked;
            },
            set(value) {
                this.$emit("onToggleisCheked", {
                    value,
                    id: this.todo.id
                });
                console.log(value);
            }
        },
        editMode() {
            return this.todo.editMode;
        }
    },
    methods: {
handleUpdateTodo(updateTodo) {
    this.$emit("onUpdateTodo", updateTodo);
},

        toggleIsChecked(event) {
            console.log(event.target.value);
        },
        toggleEditTodo() {
            this.$emit("onToggleEditTodo", this.todo.id);
        },
        removeTodo() {
            this.$emit("onRemoveTodo", this.todo.id);
        }
    },
    components: { TodoForm }
}
</script>
<style scoped>
    
</style>