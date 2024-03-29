<script setup>
import { ref } from 'vue'

const props = defineProps({
    task: { type: String, required: true },
    completed: { type: Boolean, default: false, required: false },
    tablicaIndex: { type: Number, required: true },
    taskIndex: { type: Number, required: true }
})

const emit = defineEmits(['changed'])

const finishedOrNotText = ref("")

const OnChange = () => {
    emit('changed', props.tablicaIndex, props.taskIndex)

    if (props.completed) {
        finishedOrNotText.value = "Ukończ"
    } else {
        finishedOrNotText.value = "Odznacz"
    }
}

if (!props.completed) {
    finishedOrNotText.value = "Ukończ"
} else {
    finishedOrNotText.value = "Odznacz"
}
</script>

<template>
    <li>
        <span v-if="props.completed" class="completed-task">
            <s>{{ props.task }}</s>
        </span>
        <span v-else>{{ props.task }}</span>
        <button @click="OnChange" class="toggle-button">{{ finishedOrNotText }}</button>
    </li>
</template>

<style scoped>
.completed-task {
    color: gray;
}

.toggle-button {
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 3px;
    padding: 5px 10px;
    margin-left: 10px;
    cursor: pointer;
}

.toggle-button:hover {
    background-color: #0056b3;
}
</style>
