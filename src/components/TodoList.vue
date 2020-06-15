<template>
    <div class="todo">
        <input 
        type="text" 
        class="todo__input" 
        placeholder="введите задачу"
        v-model="newTodo"
        @keyup.enter="addTodo" 
        >
        <div v-for="(todo, index) in todos" :key="todo.id" class="todo__item">
            <div class="todo__item__left">
                <div
                v-if="!todo.editing" 
                @dblclick="editTodo(todo)"
                class="todo__item__left--label"
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
    </div>
</template>

<script>
    export default {
        name: 'TodoList',
        data() {
            return {
                newTodo: '',
                idForTodo: 3,
                beforeEditCache: '',
                todos: [
                    {
                        'id': 1,
                        'title': 'buy milk',
                        completed: false,
                        editing: false, // чтобы спрятать инпут по условию
                    },
                    {
                        'id': 2,
                        'title': 'drink milk',
                        completed: false,
                        editing: false, // чтобы спрятать инпут по условию
                    }
                ]
            }
        },
        directives: {  // чтобы инпут сразу фокусился после даб клика
            focus: {
                inserted: function(el){
                    el.focus()
                }
            }
        },
        methods: {
            addTodo(){
                if(this.newTodo.trim().length == 0){ // делаем проверку чтобы не добавлять в массив пустые строки
                    return
                }
                this.todos.push({
                    id: this.idForTodo, // создаем значение которое будет присваивать id
                    title: this.newTodo,
                    completed: false
                })
                this.newTodo=''  // обнуляем поле ввода
                this.idForTodo++ // после каждого добавления в массив будем увеличиваь id чтобы он был индивидуальным
            },
            editTodo(todo) {
                this.beforeEditCache = todo.title // запоминаем значение перед изменением
                todo.editing = true  // меняем состояние на тру
            },
            doneEdit(todo){
                todo.editing = false // убираем блюр по клику или ентеру
            },
            cancelEdit(todo){    // отменяем изменения по esc
                todo.title = this.beforeEditCache
                todo.editing = false 
            },
            removeTodo(index) {
                this.todos.splice(index, 1)  // удаляем по индексу
            }
        }
    }
</script>

<style lang="scss">
    .todo {
        &__input{
            width: 100%;
            padding: 10px 16px;
        }
        &__item {
            display: flex;
            margin: 10px 0;
            &__left {
                width: 100%;
                &--label {
                    flex: 1;
                    font-size: 24px;
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
    }
</style>