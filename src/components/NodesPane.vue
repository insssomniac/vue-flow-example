<template>
  <div class="nodes">
    <div class="vue-flow__node-input" :draggable="true" @dragstart="onDragStart($event, 'input')">Input Node</div>
    <div class="vue-flow__node-default" :draggable="true" @dragstart="onDragStart($event, 'default')">Default Node</div>
    <div class="vue-flow__node-output" :draggable="true" @dragstart="onDragStart($event, 'output')">Output Node</div>
  </div>
</template>

<script setup>
const onDragStart = (event, nodeType) => {
  if (event.dataTransfer) {
    event.dataTransfer.setData('application/vueflow', nodeType)
    event.dataTransfer.effectAllowed = 'move'
  }
}
</script>

<style scoped>
.nodes {
  position: absolute;
  left: 0;
  right: 0;
  top: 10px;
  z-index: 4;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 8px;
}

.vue-flow__node-default,
.vue-flow__node-input,
.vue-flow__node-output {
  width: 70px;
  font-size: 8px;
  padding: 5px;
}

@media (max-width: 768px) {
  .nodes {
    right: 10px;
    left: auto;
  }
}

</style>
