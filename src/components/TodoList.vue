<template>
    <div class="todo">
        <input 
            type="text" 
            class="todo__input" 
            placeholder="введите задачу"
            v-model="newTodo"
            @keyup.enter="addTodo" 
        >
        <TodoItem v-for="(todo, index) in todosFiltered" :key="todo.id" :todo="todo" :index="index" @removedTodo="removeTodo" @finishedEdit="finishedEdit">
            
        </TodoItem>
        <hr>
        <div class="todo__extra">
            <label for="checkall">
                <input 
                    type="checkbox" 
                    id="checkall" 
                    :checked="!anyRemaining"
                    @change="checkAllTodos"
                >Check All
            </label>
            <div> {{remaining}} items left</div>
        </div>
        <div class="todo__extra">
            <button :class="{active : filter == 'all'}" @click="filter ='all'">All</button>
            <button :class="{active: filter == 'active'}" @click="filter ='active'">Active</button>
            <button :class="{active: filter == 'completed'}" @click="filter ='completed'">Completed</button>
        </div>
        <div>
            <transition name="fade">
                <button v-show="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
            </transition>    
        </div>
    </div>
</template>

<script>
    import TodoItem from './TodoItem.vue'

    export default {
        name: 'TodoList',
        components: {
            TodoItem,
        },
        data() {
            return {
                newTodo: '',
                idForTodo: 3, //хардкодим чтобы начинать отсчет от этой цифры
                beforeEditCache: '',
                filter: 'all',
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
        computed: { // всегда чтото возвращает, не мутируем
            remaining(){
                return this.todos.filter( todo => !todo.completed).length
            },
            anyRemaining(){ // если все чекбоксы выбраны то и checkall выбран
                return this.remaining != 0
            },
            todosFiltered(){
                if(this.filter == 'all'){
                    return this.todos
                }else if(this.filter == 'active'){
                   return this.todos.filter(todo => !todo.completed)
                }else if(this.filter == 'completed'){
                   return this.todos.filter(todo => todo.completed)
                }
                return this.todos
            },
            showClearCompletedButton(){
               return this.todos.filter(todo => todo.completed).length >0 // если есть хоть 1 завершеннач задача т опоявится эта кнопка
            }
            
        },
        directives: {  // чтобы инпут сразу фокусился после даблклика
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
                if(todo.title.trim() == ''){ // делаем проверку чтобы при изменении не сохранялась пустая строка
                    todo.title = this.beforeEditCache
                }
                todo.editing = false // убираем блюр по клику или ентеру
            },
            cancelEdit(todo){    // отменяем изменения по esc
                todo.title = this.beforeEditCache
                todo.editing = false 
            },
            removeTodo(index) {
                this.todos.splice(index, 1)  // удаляем по индексу
            },
            checkAllTodos(){ // делает все туду сделаными
                this.todos.forEach((todo) => todo.completed = event.target.checked)
            },
            clearCompleted(){
                this.todos = this.todos.filter(todo => !todo.completed)
            },
            finishedEdit(data){ // data берется из todoitem объект
                this.todos.splice(data.index, 1, data.todo)
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
        &__extra {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
    }

    .completed {
        text-decoration: line-through;
    }

    .active {
        background: green;
        outline: none;
        border: 0px;
    }

    .fade-enter-active, .fade-leave-active {
        transition: opacity .2s;
    }

    .fade-enter, .fade-leave-to {
        opacity: 0;
    }
</style>