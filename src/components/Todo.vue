<template>
<div>
    <button @click="showTodoItemsWhich('all')" :class="{buttonClicked: visibility == 'all'}">全部</button>
    <button @click="showTodoItemsWhich('selected')" :class="{buttonClicked: visibility == 'selected'}">选择</button>
    <button @click="showTodoItemsWhich('unselected')" :class="{buttonClicked: visibility == 'unselected'}">未选择</button>
    <button @click="showTodoItemsWhich('complete')" :class="{buttonClicked: visibility == 'complete'}">完成</button>
    <button @click="showTodoItemsWhich('uncomplete')" :class="{buttonClicked: visibility == 'uncomplete'}">未完成</button>
    <p v-text="filteredTodos.length"></p>
    <h1 v-text="title"></h1>
    <input type="text" v-model="newTodoItem" @keyup.enter="addNewTodoItem()">
    <button @click="completeAllTotoItems()">complete all</button>
    <button @click="deleteAllTotoItems()">delete all</button>
    <button @click="deleteAllCompleteItems()">delete complete</button>
    <button @click="deleteAllCSelectedItems()">delete selected</button>
    <ul>
        <li v-for="(todoItem, todoIndex) in filteredTodos">
            <div class="selector" :class="{selected: todoItem.selected}" @click="selectCurrent(todoIndex)"></div>
            <div v-text="todoItem.content"
                 :class="{complete: todoItem.complete}"
                 @click="toggleCurrentTodoItem(todoIndex)"></div>
            <div @click="deleteCurrentTodoItem(todoIndex)">删除</div>
        </li>
    </ul>
</div>
</template>

<script>
var todoLocalKey = 'todoLocalItems'
var todoStorage = {
    fetch: function (STORAGE_KEY) {
        var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
        todos.forEach(function (todo, index) {
            todo.id = index
        })
        todoStorage.uid = todos.length
        return todos
    },
    save: function (STORAGE_KEY, todos) {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
    }
}


var filters = {
    all: function (todos) {
        return todos
    },
    uncomplete: function (todos) {
        return todos.filter(function (todo) {
            return !todo.complete
        })
    },
    complete: function (todos) {
        return todos.filter(function (todo) {
            return todo.complete
        })
    },
    unselected: function (todos) {
        return todos.filter(function (todo) {
            return !todo.selected
        })
    },
    selected: function (todos) {
        return todos.filter(function (todo) {
            return todo.selected
        })
    }
}

export default {
  name: 'todo',
  data () {
    return {
      title: 'a vue todo list for myself',
      visibility: 'all',
      newTodoItem: '',
      todoItems: todoStorage.fetch(todoLocalKey)
    }
  },
  methods: {
        addNewTodoItem: function () {
            console.log(this.newTodoItem)
            if (this.newTodoItem == '') return;
            var newTodoItem = {
                content: this.newTodoItem,
                complete: false,
                selected: false
            };

            this.newTodoItem = '';
            this.todoItems.unshift(newTodoItem);
        },
        deleteCurrentTodoItem: function (index) {
            this.todoItems.splice(index, 1);
        },
        toggleCurrentTodoItem: function (index) {
            this.todoItems[index].complete = !this.todoItems[index].complete;
        },
        completeAllTotoItems: function () {
            this.todoItems.forEach(function (todo) {
                todo.complete = true;
            })
        },
        deleteAllTotoItems: function () {
            this.todoItems = [];
        },
        deleteAllCompleteItems: function () {
            this.todoItems = filters.uncomplete(this.todoItems);
        },
        selectCurrent: function (index) {
            this.todoItems[index].selected = !this.todoItems[index].selected;
        },
        deleteAllCSelectedItems: function () {
            this.todoItems = filters.unselected(this.todoItems);
        },
        showTodoItemsWhich: function (visibility) {
            this.visibility = visibility
        }
    },
    computed: {
        filteredTodos: function () {
            return filters[this.visibility](this.todoItems)
        },
    },
    watch: {
        todoItems: {
            handler: function (items) {
                todoStorage.save(todoLocalKey, items)
            },
            deep: true
        }
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

h1{
    font-size: 20px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.complete{
    text-decoration: line-through;
    color: #989797;
}

.selector{
    height: 20px;
    width: 20px;
    background: black;
}

.selector.selected{
    background: green;
}
.buttonClicked{
    background: #5fb7c1;
}
li{
    border-bottom: 1px solid black;
    margin-bottom: 10px;
}
</style>
