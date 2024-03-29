<script setup>
import { ref } from 'vue';

    const props = defineProps({
        header: {type: String, required: true},
        open: {type: Boolean, default: false},
        name: {type: String, required: true}
    });

    const isOpen = ref(props.open);

    const SwitchOpen = () => {
        isOpen.value = !isOpen.value;
    }

    const collapse = () => {
        isOpen.value = false;
    }

    defineExpose({collapse});

</script>
<template>
    <div>
        <h2>
            {{ props.name }}
            <slot name="header">{{ props.header }}</slot>
            <button @click="SwitchOpen">X</button>
        </h2>
        <div v-if="isOpen">
            <slot></slot>
        </div>
    </div>
    
</template>