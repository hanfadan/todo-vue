<template>
        <form @submit.prevent='submit'>
        <input placeholder='title' v-model="title"/>
        <input placeholder='description' v-model="description" />
        <button type='submit'>{{ isEditMode ? 'Save' : 'Add' }}</button>
        </form>
</template>
<script>
export default {
    name: 'TodoForm',

    data() {
        return {
            title: '',
            description: '',
        }
    },

    props: {
        todo: {
            type: Object,
            default: () => ({})
        },
            isEditMode: {
                type: Boolean,
                default: false
            }
        },


    created() {
        if (this.isEditMode) {
            this.title = this.todo.title;
            this.description = this.todo.description;
            return
        }
    },

    methods: {
        submit() {

            if (this.isEditMode) {
                this.emitUpdateTodo()
            return
        }
            this.emitNewTodo()
            // console.log(this.title)
            // console.log (this.description)
            // console.log('hey')
        },

emitUpdateTodo() {
    alert('update')
    this.$emit('onUpdateTodo', {
        title: this.title,
        description: this.description,
        id: this.todo.id,
        isChecked: this.todo.isChecked,
        editMode: false
    })
},

        emitNewTodo() {
            this.$emit('onNewTodo', {
                title: this.title,
                description: this.description,
                id: `todo_${Math.random()+ 100000}`,
                isCheched: false,
                editMode: false
            }),
            this.title = '',
            this.description = ''
        }
    },
}
</script>
<style scoped> 
    
</style>