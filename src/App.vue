<script setup>
import ScrollNumber from "@/component/scrollNumber.vue";
import ScrollNumberReverse from "@/component/ScrollNumberReverse.vue";
import ScrollNumberCanvas from "@/component/scrollNumberCanvas.vue";
import {ref} from "vue";

const number = ref(0)
const handleClick = () => {
	number.value = Math.random() * Math.pow(10, Math.floor((Math.random() * 10)))
}

</script>

<template>
	<button @click="handleClick">点击修改数值</button>
	<p>css版本往⬆️滚动</p>
	<ScrollNumber :modelValue="number"/>
	<p>css版本往⬇️滚动</p>
	<ScrollNumberReverse :modelValue="number"/>
	<p>canvas版本</p>
	<ScrollNumberCanvas :modelValue="number" />
</template>

<style scoped>

</style>