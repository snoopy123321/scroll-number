<script setup>
import { computed, ref, onMounted, watch, onBeforeUnmount } from "vue";

const props = defineProps({
  modelValue: { type: [Number, String], required: true },
  duration: { type: Number, default: 1 }, // 动画持续时间（秒）
  height: { type: Number, default: 30 },  // 数字高度
  width: { type: Number, default: 10 },   // 数字宽度
  fontSize: { type: Number, default: 14 }, // 字体大小
  color: { type: String, default: '#000' }, // 字体颜色
  fontFamily: { type: String, default: 'Arial' }, // 字体类型
  easing: { type: String, default: 'ease-in-out' } // 缓动函数
});

const canvasRef = ref(null);
const ctx = ref(null);
const animationFrame = ref(null);
const startTime = ref(null);
const previousValue = ref('');

const digits = computed(() => String(props.modelValue).split(''));

// 缓动函数
const getEasing = (t) => {
  switch (props.easing) {
    case 'linear':
      return t;
    case 'ease-in':
      return t * t;
    case 'ease-out':
      return t * (2 - t);
    case 'ease-in-out':
    default:
      return t < 0.5 ? 2 * t * t : -1 + (4 - 2 * t) * t;
  }
};

const initCanvas = () => {
  const canvas = canvasRef.value;
  const dpr = window.devicePixelRatio || 1;
  const width = props.width * digits.value.length;
  
  canvas.width = width * dpr;
  canvas.height = props.height * dpr;
  canvas.style.width = `${width}px`;
  canvas.style.height = `${props.height}px`;
  
  ctx.value = canvas.getContext('2d');
  ctx.value.scale(dpr, dpr);
  ctx.value.font = `${props.fontSize}px ${props.fontFamily}`;
  ctx.value.textAlign = 'center';
  ctx.value.textBaseline = 'middle';
  ctx.value.fillStyle = props.color;
};

const drawDigit = (digit, index, progress) => {
  if (!ctx.value) return;
  
  const x = index * props.width + props.width / 2;
  const y = props.height / 2;
  
  if (isNaN(digit)) {
    // 非数字字符直接绘制
    ctx.value.fillText(digit, x, y);
    return;
  }

  const currentNum = Number(digit);
  
  // 应用缓动函数
  const easedProgress = getEasing(progress);
  
  // 计算当前位置
  const totalDistance = currentNum * props.height;
  const currentOffset = totalDistance * easedProgress;
  const currentPosition = Math.floor(currentOffset / props.height);
  const remainingOffset = currentOffset % props.height;
  
  // 绘制当前可见的数字
  ctx.value.fillText(
    String(Math.min(currentPosition, currentNum)),
    x,
    y + remainingOffset
  );
  
  // 如果不是最后一个数字，绘制下一个数字
  if (currentPosition < currentNum) {
    ctx.value.fillText(
      String(currentPosition + 1),
      x,
      y + remainingOffset - props.height
    );
  }
};

const animate = (timestamp) => {
  if (!startTime.value) startTime.value = timestamp;
  const progress = Math.min((timestamp - startTime.value) / (props.duration * 1000), 1);
  
  // 清除画布
  ctx.value.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height);
  
  // 绘制每个数字
  digits.value.forEach((digit, index) => {
    drawDigit(digit, index, progress);
  });
  
  if (progress < 1) {
    animationFrame.value = requestAnimationFrame(animate);
  } else {
    previousValue.value = digits.value.join('');
  }
};

const startAnimation = () => {
  if (animationFrame.value) {
    cancelAnimationFrame(animationFrame.value);
  }
  startTime.value = null;
  animationFrame.value = requestAnimationFrame(animate);
};

onMounted(() => {
  initCanvas();
  // 修改这里：确保 previousValue 是字符串
  previousValue.value = String(props.modelValue);
  startAnimation();
});
// 修改 watch 函数，确保在值变化时重新初始化画布
watch(() => props.modelValue, () => {
  initCanvas(); // 重新初始化画布以适应新的数字长度
  startAnimation();
});

// 在组件销毁时清理动画帧
onBeforeUnmount(() => {
  if (animationFrame.value) {
    cancelAnimationFrame(animationFrame.value);
  }
});
</script>

<template>
  <canvas ref="canvasRef"></canvas>
</template>

<style scoped>
canvas {
  display: block;
}
</style>