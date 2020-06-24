<template>
    <div class="todo__item">
        <div class="todo__item__left">
            <input 
                type="checkbox" 
                v-model="todo.completed"
            >
            <div
                v-if="!todo.editing" 
                @dblclick="editTodo(todo)"
                class="todo__item__left--label"
                :class="{completed : todo.completed}"
            >{{todo.title}}</div>
            <input 
                v-else
                v-focus
                type="text" 
                v-model="todo.title"
                @blur="doneEdit(todo)"
                @keyup.enter="doneEdit(todo)"
                @keyup.esc="cancelEdit(todo)"
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