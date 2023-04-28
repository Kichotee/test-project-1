<template>
  <div
    :style="{ left: position.x + 'px', top: position.y + 'px' }"
    @mousedown.stop="onMouseDown"
    class="container"
  >
    <span class="label">Content Box</span>
  </div>
</template>

<script lang="ts" setup>
import {  reactive, ref, computed } from "vue";

interface Props {
  parentPosition: { x: number; y: number };
  parentWidth: number;
  parentHeight: number;
}

const props = defineProps<Props>();


const position = reactive({ x: 0, y: 0, width: 100, height: 100 });
const isDragging = ref(false);
const mouseOffset = reactive({ x: 0, y: 0 });

const containerWidth = computed(() => {
  return props.containerWidth;
});

const containerHeight = computed(() => {
  return props.containerHeight;
});

const maxPosition = computed(() => {
  return {
    x: containerWidth.value - position.width,
    y: containerHeight.value - position.height,
  };
});

const onMouseDown = (event: MouseEvent) => {
  isDragging.value = true;
  mouseOffset.x = event.pageX - props.parentPosition.x;
  mouseOffset.y = event.pageY - props.parentPosition.y;
  window.addEventListener("mousemove", onMouseMove);
  window.addEventListener("mouseup", onMouseUp);
  event.preventDefault();
};

const onMouseMove = (event: MouseEvent) => {
  if (isDragging.value) {
    const newPosition = {
      x: event.pageX - mouseOffset.x - props.parentPosition.x,
      y: event.pageY - mouseOffset.y - props.parentPosition.y,
    };
    position.x = Math.min(Math.max(newPosition.x, 0), maxPosition.value.x);
    position.y = Math.min(Math.max(newPosition.y, 0), maxPosition.value.y);
  }
};

const onMouseUp = () => {
  isDragging.value = false;
  window.removeEventListener("mousemove", onMouseMove);
  window.removeEventListener("mouseup", onMouseUp);
};
</script>

<style lang="postcss" scoped>
.container {
  @apropsly bg-blue-500 h-[80px] w-[80px] absolute
}
.label {
  @apply absolute -top-3 px-3 left-0 bg-black text-white text-xs;
}
</style>
