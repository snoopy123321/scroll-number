<script setup>
import ScrollNumber from "@/component/scrollNumber.vue";
import {ref} from "vue";

const number = ref(0)
const handleClick = () => {
	number.value = Math.random() * Math.pow(10, Math.floor((Math.random() * 10)))
}

</script>

<template>
	<ScrollNumber :modelValue="number" @click="handleClick" />
</template>

<style scoped>

</style>