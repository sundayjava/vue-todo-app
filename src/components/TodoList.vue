<script setup>
import { computed, ref } from 'vue'
import TodoListItem from './TodoListItem.vue'

let id = 0
const filter = ref('all')
const newTodo = ref('')

const todos = ref([
    {
        id: id++,
        title: "Finish vue Screencast",
        completed: false,
        editing: false
    },
    {
        id: id++,
        title: "Take over world",
        completed: false,
        editing: false
    },
])

function addTodo() {
    if (newTodo.value.trim().length === 0) { return };

    todos.value.push({ id: id++, title: newTodo.value, completed: false, editing: false })
    newTodo.value = ''
}

function removeTodo(index) {
    todos.value.splice(index, 1)
}

const remaining = computed(() => todos.value.filter((t) => !t.completed).length)
const anyRemaining = computed(() => todos.value.filter((t) => !t.completed).length !== 0)
const todosFilters = computed(() => {
    if (filter.value === 'all') {
        return todos.value;
    } else if (filter.value === 'active') {
        return todos.value.filter((t) => !t.completed);
    } else if (filter.value === 'completed') {
        return todos.value.filter((t) => t.completed);
    }

    return todos
})

const showClearCompletedButton = computed(() => todos.value.filter((t) => t.completed).length > 0)

function checkAllTodos(event) {
    todos.value.forEach((todo) => todo.completed = event.target.checked)
}

function clearCompleted() {
    todos.value = todos.value.filter((t) => !t.completed)
}

function finishEdit(data) {
    const todo = todos.value.find((t) => t.id === data.todo.id)
    if (todo) {
        Object.assign(todo, data.todo)
    }
}
</script>

<template>
    <div>
        <input type="text" class="todo-input" placeholder="what needs to be done!" v-model="newTodo"
            @keyup.enter="addTodo">
        <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">

            <TodoListItem v-for="(todo, index) in todosFilters" :key="todo.id" :todo="todo" :index="index"
                @removedTodo="removeTodo" :checkAll="!anyRemaining" @finishedEdit="finishEdit" />

        </transition-group>
        <div class="extra-container">
            <div>
                <label>
                    <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All
                </label>
            </div>
            <div>{{ remaining }} Items left</div>
        </div>
        <div class="extra-container">
            <div>
                <button :class="{ active: filter == 'all' }" @click="filter = `all`">All</button>
                <button :class="{ active: filter == 'active' }" @click="filter = `active`">Active</button>
                <button :class="{ active: filter == 'completed' }" @click="filter = `completed`">Completed</button>
            </div>
            <div>
                <transition name="fade">
                    <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
                </transition>
            </div>
        </div>
    </div>
</template>

<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css");

.todo-input {
    width: 100%;
    padding: 10px 10px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus {
        outline: none;
    }
}

.todo-items {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
}

.remove-item {
    cursor: pointer;
    margin-left: 14px;

    &:hover {
        color: red;
    }
}

.todo-item-left {
    display: flex;
    align-items: center;
}

.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}

.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;

    &:focus {
        outline: none;
    }
}

.completed {
    text-decoration: line-through;
    color: gray;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-top: 14px;
}

button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    cursor: pointer;

    &:hover {
        background: lightgreen;
    }

    &:focus {
        outline: none;
    }
}

.active {
    background: lightgreen;
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity .4s;
}

.fade-enter,
.fade-leave-to {
    opacity: 0;
}
</style>