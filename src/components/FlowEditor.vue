<template>
  <VueFlow
      class="basicflow"
      :min-zoom="0.2"
      :max-zoom="4"
      @dragover="onDragOver"
      @drop="onDrop">
    <Background pattern-color="#aaa" gap="5"/>
    <MiniMap/>
    <Controls>
      <ControlButton v-on:click="logToObject">
        <b-icon-save/>
      </ControlButton>
    </Controls>
    <NodesPane/>
    <div class="updatenode__controls">
      <label>Edit Label:</label>
      <input v-model="opts.label" @input="updateNode"/>
    </div>
    <div class="select-edge-type__controls">
      <label class="select-edge-type__label">New edge type:</label>
      <div class="select-edge-type__box">
        <input type="radio" id="one" value="yes" v-model="opts.edgeType">
        <label for="one" class="yes">Yes</label>
      </div>
      <div class="select-edge-type__box">
        <input type="radio" id="two" value="no" v-model="opts.edgeType">
        <label for="two" class="no">No</label>
      </div>

    </div>
  </VueFlow>
</template>

<script setup>
import {Background, Controls, ControlButton, MiniMap, VueFlow, useVueFlow} from '@braks/vue-flow'
import {reactive} from 'vue'
import NodesPane from './NodesPane';
import {BIconSave} from 'bootstrap-icons-vue';

const {
  onNodeDoubleClick,
  onNodeClick,
  onPaneClick,
  onEdgeClick,
  toObject,
  onConnect,
  addNodes,
  addEdges,
  getSelectedNodes,
  applyEdgeChanges,
  applyNodeChanges,
  project
} = useVueFlow()

const edgeTypes = {
  typeYes: {
    type: 'smoothstep',
    style: {stroke: 'green'},
    label: 'yes',
  },
  typeNo: {
    type: 'smoothstep',
    style: {stroke: 'orange'},
    label: 'no',
  }
}

const opts = reactive({
  label: '',
  edgeType: 'yes'
})

let id = 0

const getId = () => `node_${id++}`

const onDragOver = (event) => {
  event.preventDefault()
  if (event.dataTransfer) {
    event.dataTransfer.dropEffect = 'move'
  }
}

const onDrop = (event) => {
  const type = event.dataTransfer?.getData('application/vueflow')
  const position = project({x: event.clientX - 80, y: event.clientY - 18})
  const newNode = {
    id: getId(),
    type,
    position,
    label: `${type} node`,
  }
  addNodes([newNode])
}

const updateNode = () => {
  const node = getSelectedNodes.value[0]
  if (node) {
    node.label = opts.label.trim() !== '' ? opts.label.trim() : ''
  }
}

onConnect((params, type = opts.edgeType) => {
  if (type === 'yes') Object.assign(params, edgeTypes.typeYes)
  if (type === 'no') Object.assign(params, edgeTypes.typeNo)
  params.updatable = true
  addEdges([params])
})

onNodeDoubleClick((e) => {
  if (confirm('Delete node?')) {
    applyNodeChanges([{type: 'remove', id: e.node.id}])
  }
})

onEdgeClick((e) => {
  if (confirm('Delete edge?')) {
    applyEdgeChanges([{ type: 'remove', id: e.edge.id }])
  }
})

onNodeClick((e) => {
  opts.label = e.node.label
})

onPaneClick(() => {
  opts.label = ''
})

const logToObject = () => console.log(toObject())

</script>

<style scoped>

.updatenode__controls {
  position: absolute;
  left: 10px;
  top: 10px;
  z-index: 4;
  font-size: 10px;
  border: #d3d3d3 1px solid;
  border-radius: 10px;
  padding: 8px;
  width: 25%;
}

.updatenode__controls label, .select-edge-type__label {
  display: block
}

.updatenode__controls input {
  padding: 2px;
  border-radius: 5px;
  width: 96%;
}

.select-edge-type__controls {
  position: absolute;
  left: 10px;
  top: 70px;
  z-index: 4;
  font-size: 10px;
  border: #d3d3d3 1px solid;
  border-radius: 10px;
  padding: 8px;
  width: 90px;
  text-align: left;
}

.select-edge-type__controls input {
  margin: 0 4px 0 0;
}

.select-edge-type__box {
  display: flex;
  align-items: center;
  margin-top: 5px;
}

.select-edge-type__controls .yes {
  background-color: lightgreen;
}

.select-edge-type__controls .no {
  background-color: orange;
}

</style>
