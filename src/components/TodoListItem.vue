<script setup>
import { reactive, defineProps, defineEmits, watch } from 'vue';

const props = defineProps({
    todo: {
        type: Object,
        required: true
    },
    index: {
        type: Number,
        required: true
    },
    checkAll: {
        type: Boolean,
        required: true
    }
});

const vFocus = {
    mounted: (el) => el.focus()
}

const emit = defineEmits(['finishedEdit'])

const state = reactive({
    id: props.todo.id,
    title: props.todo.title,
    completed: props.todo.completed,
    editing: props.todo.editing,
    beforeEditCache: ''
});

watch(() => {
    // if (props.checkAll) {
    //     state.completed = true;
    // } else {
    //     state.completed = props.todo.completed
    // }

    state.completed = props.checkAll ? true : props.todo.completed
});

console.log("Out props is: ", state.id)

function editTodo() {
    state.beforeEditCache = state.title
    state.editing = true
}

function doneEdit() {
    if (state.title.trim() === '') {
        state.title = state.beforeEditCache
    };
    state.editing = false

    emit('finishedEdit', {
        index: state.index,
        todo: {
            id: state.id,
            title: state.title,
            completed: state.completed,
            editing: state.editing,
        }
    })
}

function cancelEdit() {
    state.title = state.beforeEditCache
    state.editing = false
}

</script>

<template>
    <div class="todo-items">
        <div class="todo-item-left">
            <input type="checkbox" v-model="state.completed" @change="doneEdit">
            <div class="todo-item-label" :class="{ completed: state.completed }" v-if="!state.editing"
                @dblclick="editTodo">{{ state.title }}</div>
            <input class="todo-item-edit" v-else type="text" v-model="state.title" @blur="doneEdit"
                @keyup.enter="doneEdit" v-focus @keyup.esc="cancelEdit">
        </div>
        <div class="remove-item" @click="$emit('removedTodo', index)">
            &times;
        </div>
    </div>
</template>
