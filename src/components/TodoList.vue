<template>
    <div class="todo">
        <input 
        type="text" 
        class="todo__input" 
        placeholder="введите задачу"
        v-model="newTodo"
        @keyup.enter="addTodo" 
        >
        <div v-for="todo in todos" :key="todo.id" class="todo__item">
            <div class="todo__item__left">{{todo.title}}</div>
            <div>&times;</div>
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
                todos: [
                    {
                        'id': 1,
                        'title': 'buy milk',
                        completed: false,
                    },
                    {
                        'id': 2,
                        'title': 'drink milk',
                        completed: false,
                    }
                ]
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
                flex: 1;
            }
        }
    }
</style>