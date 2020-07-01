<template>
    <div class="todo__item">
        <div class="todo__item__left">
            <input 
                type="checkbox" 
                v-model="completed"
            >
            <div
                v-if="!editing" 
                @dblclick="editTodo"
                class="todo__item__left--label"
                :class="{completed : completed}"
            >{{title}}</div>
            <input 
                v-else
                v-focus
                type="text" 
                v-model="title"
                @blur="doneEdit"
                @keyup.enter="doneEdit"
                @keyup.esc="cancelEdit"
                class="todo__item__left--edit"
            >  
        </div>
        <div 
            class="todo__item__remove"
            @click="removeTodo(index)"
        >&times;</div>
    </div>
</template>

<script>
export default {
    name: 'todo-item',
    props: { // то что приходит от родителя
        todo: {
            type: Object,
            required: true,
        },
        index: {
            type: Number,
            required: true,
        }
    },
    data(){
        return {
            'id': this.todo.id,
            'title': this.todo.title,
            'completed': this.todo.completed,
            'editing': this.todo.editing,
            'beforeEditCache': ''
        }
    },
    methods: {
        removeTodo(index){ // передадим родителю так как у нас больше нет доступа к массиву todo, а у родителчя есть
            this.$emit('removedTodo', index) // необходимо в родителе(в теге TodoItem) обозначить @removedTodo="removeTodo"
        },
        editTodo() {
            this.beforeEditCache = this.title // берется из todolist, так как у нас локально теперь то вместо todo. пишем this.
            this.editing = true  // берется из todolist, так как у нас локально теперь то вместо todo. пишем this.
        },
        doneEdit(){
                if(this.title.trim() == ''){ 
                this.title = this.beforeEditCache
            }
            this.editing = false
            this.$emit('finishedEdit', {
                'index': this.index,
                'todo' : {
                    'id': this.id,
                    'title': this.title,
                    'completed': this.completed,
                    'editing': this.editing,
                }
            }) 
        },
        cancelEdit(){   
            this.title = this.beforeEditCache
            this.editing = false 
        },
    }
}
</script>

<style lang="scss">
.todo__item {
    display: flex;
    margin: 10px 0;
    &__left {
        width: 100%;
        display: flex;
        align-items: center;
        &--label {
            flex: 1;
            font-size: 24px;
            padding-left: 10px;
        }
        &--edit {
            width: 100%;
            font-size: 24px;
            font-family:'Times New Roman', Times, serif;
            &:focus {
                outline: none;
            }
        }
    }
    &__remove {
        cursor: pointer;
        &:hover {
            color: red;
        }
    }
}

</style>