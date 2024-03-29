<script setup>
import { defineEmits, ref } from 'vue'

const emit = defineEmits(['create'])
const newTaskText = ref("")

const OnCreated = () => {
    if (newTaskText.value.trim() !== "") {
        emit('create', newTaskText.value.trim())
        newTaskText.value = ""
    }
}
</script>

<template>
    <div>
        <input v-model="newTaskText" @keydown.enter="OnCreated" type="text" placeholder="Wpisz swoje zdanie"
            class="task-input">
        <button @click="OnCreated" class="add-button">Dodaj</button>
    </div>
</template>

<style scoped>
.task-input {
    padding: 8px;
    margin-right: 10px;
    border: 1px solid #ced4da;
    border-radius: 3px;
}

.add-button {
    background-color: #28a745;
    color: #fff;
    border: none;
    border-radius: 3px;
    padding: 8px 12px;
    cursor: pointer;
}

.add-button:hover {
    background-color: #218838;
}
</style>
