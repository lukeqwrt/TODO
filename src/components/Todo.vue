<script setup>
    const props = defineProps(['todos'])

</script>

<template>
    <div class="todo_item_wrapper">
        <div class="todo_item" v-for="(todo,index) in todos" :key="todo">
            <div class="check">
                <label>
                <input class="checkbox" type="checkbox" v-model="todo.done">
                <div class="bubble" :class="{ 'todocheck' : todo.done }">
                    <img src="../assets/images/icon-check.svg" alt="">
                </div>
                </label>
            </div>
            <input type="text" v-model="todo.content" :class="{ 'cross' : todo.done }" >
            <img @click="$emit('delete', index)" src="../assets/images/icon-cross.svg" alt="" class="delete">
        </div>
    </div>
</template>

<style lang="scss">
</style>
