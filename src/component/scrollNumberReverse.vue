<script setup>
import { computed, ref, watch, nextTick } from "vue";

const props = defineProps({
  modelValue: { type: [Number, String], required: true },
  duration: { type: Number, default: 1 }, // 动画持续时间（秒）
  height: { type: Number, default: 30 },  // 数字高度
  width: { type: Number, default: 10 }    // 数字宽度
});

const getStr = computed(() => {
  return String(props.modelValue).split('');
});

const numRef = ref([]);

// 执行数字滚动动画
const animateNumbers = async () => {
	// 等待DOM更新为0
  await nextTick()

if (!numRef.value || numRef.value.length === 0) return;

  numRef.value.forEach(item => {
  	  if (!item) return;
      const targetPosition = (item.children.length - 1) * props.height;
  	  item.style.transform = `translateY(-${targetPosition}px)`;

		setTimeout(() => {
  	  		item.style.transform = `translateY(0px)`;
  	  		item.style.transitionDuration = `${props.duration}s`;
          setTimeout(() => {
            item.style.transitionDuration = `0s`;
          }, 100)
		}, 100)
  	});
};

// 监听数值变化，触发动画
watch(() => props.modelValue, () => {
  animateNumbers();
}, { immediate: true });
</script>

<template>
  <div class="container" :style="{ height: `${props.height}px` }">
    <div 
      v-for="(item, idx) in getStr" 
      :key="idx"
      class="digit-container" 
      :style="{ height: `${props.height}px` }"
    >
      <div
        v-if="!isNaN(item)"
        class="number-column"
        :style="{
           transform: `translateY(-${Number(item) * props.height}px)`
        }"
        ref="numRef"
      >
        <div 
          v-for="index in Number(item) + 1" 
          :key="index - 1"
          class="digit"
          :style="{ 
            height: `${props.height}px`, 
            width: `${props.width}px` ,
        
          }"
        >
          {{ index - 1 }}
        </div>
      </div>
      <div 
        v-else 
        class="digit"
        :style="{ 
          height: `${props.height}px`, 
          width: `${props.width}px` 
        }"
      >
        {{ item }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  overflow: hidden;
}

.number-column {
  display: flex;
  flex-direction: column-reverse;
}

.digit {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>