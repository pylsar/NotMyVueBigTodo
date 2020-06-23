<template>
    <div class="todo">
        <input 
            type="text" 
            class="todo__input" 
            placeholder="введите задачу"
            v-model="newTodo"
            @keyup.enter="addTodo" 
        >
        <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo__item">
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
            <button v-show="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>

        </div>
    </div>
</template>

<script>
    export default {
        name: 'TodoList',
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
</style>