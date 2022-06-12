<template>
  <div
    class="blockTable"
  >
    <div class="parent-div">
      <div
        class="parent-div__b1"
        @click="showChildren"
        :style="{
          marginLeft: level > 0 ? level * 5 + 'px' : 0,
          cursor: parent.child.length > 0 ? 'pointer' : '',
          backgroundColor: index % 2 === 0 ? '#e9fff0' : '#d1d7ff'
        }"
      >
        {{
          (parent.child.length > 0 ?
            !parent.showChild ? " + " : " - "
          : "") +
          parent.name
        }}
      </div>
      <div
        class="parent-div__b2"
        :style="{
          borderTop: level > 0 ? 'none' : '',
          backgroundColor: index % 2 === 0 ? '#e9fff0' : '#d1d7ff'
        }"
      >{{parent.phoneFormat}}</div>
    </div>
    <div v-if="parent.showChild">
      <ItemTable
        v-for="(child, index) in parent.child"
        :key="child.id"
        :parent="child"
        :index="index"
        :level="level > 0 ? level + 1: 1"
      />
    </div>
  </div>
</template>

<script>
import ItemTable from './tableItem.vue'

export default {
  name: 'ItemTable',
  components: {
    ItemTable
  },
  props: {
    parent: {
      type: Object,
      required: true
    },
    level: {
      type: Number,
      default: 0
    },
    index: {
      type: Number
    }
  },
  methods: {
    showChildren () {
      if (this.parent.child.length > 0) {
        this.parent.showChild = !this.parent.showChild
      }
    }
  }
}
</script>

<style scoped>

.blockTable {
  width: 100%;
}
.parent-div {
  width: 100%;
  border-bottom:none;
  display: flex;
  justify-content: space-between;
}

.parent-div__b1 {
  width: 100%;
  padding: 10px;
  border-left: 1px solid black;
  border-bottom: 1px solid black;
}

.parent-div__b1:first-child {
  border-top: 1px solid black;
}

.parent-div__b2 {
  border: 1px solid black;
  width: 30%;
  padding: 10px;
}

</style>
