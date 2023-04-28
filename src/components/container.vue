<template>
  <div
    :style="{ left: position.x + 'px', top: position.y + 'px' }"
    @mousedown="onMouseDown"
    class="container"
    ref="container"
  >
    <span class="label">Container Box</span>
    <content
      :parentPosition="position"
      :parentHeight="containerHeight"
      :parentWidth="containerWidth"
    />
  </div>
</template>

<script lang='ts' setup>
import { nextTick, ref, reactive, computed, onMounted } from "vue";
import content from "./content.vue";

// 
const position = reactive({ x: 600, y: 100 });
const mouseOffset = reactive({ x: 0, y: 0 });
const dragElement = ref(false);
const containerWidth = ref(0);
const containerHeight = ref(0);
const container = ref<HTMLElement | any>(null);



const maxPosition = computed(() => {
  return {
    x: window.innerWidth - containerWidth.value,
    y: window.innerHeight - containerHeight.value,
  };
});


const onMouseDown = (event: MouseEvent) => {
  dragElement.value = true;
  mouseOffset.x = event.pageX - position.x;
  mouseOffset.y = event.pageY - position.y;
  window.addEventListener("mousemove", onMouseMove);
  window.addEventListener("mouseup", onMouseUp);
};


const onMouseMove = (event: MouseEvent) => {
  if (dragElement.value) {
    const newPosition = {
      x: event.pageX - mouseOffset.x,
      y: event.pageY - mouseOffset.y,
    };
    position.x = Math.min(Math.max(newPosition.x, 0), maxPosition.value.x);
    position.y = Math.min(Math.max(newPosition.y, 0), maxPosition.value.y);
  }
};

const onMouseUp = () => {
  dragElement.value = false;
  window.removeEventListener("mousemove", onMouseMove);
  window.removeEventListener("mouseup", onMouseUp);
};

onMounted(() => {
   nextTick(() => {
      containerWidth.value = container.value.clientWidth;
      containerHeight.value =container.value.clientHeight;
    });

})


</script>

<style lang='postcss' scoped>
.container {
  @apply w-[300px] absolute h-[300px] bg-gray-300;
}

.label {
  @apply absolute -top-3 px-3 left-0 bg-gray-300 text-black text-xs;
}
</style>