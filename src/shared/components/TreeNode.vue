<template>
  <div
    @click="toggleExpand"
    :style="{ paddingLeft: `${20 + 20 * level}px` }"
    class="TreeNode"
    :class="{ hasChildren, isExpanded }"
  >
    <span :class="iconClass"></span>
    {{ node.title }}
  </div>
  <template v-if="isExpanded">
    <TreeNode
      v-for="child in childNodes"
      :key="child.id"
      :node="child"
      :nodes="nodes"
      :expanded-nodes="expandedNodes"
    />
  </template>
</template>

<script setup>
import { computed, ref } from "vue";

const props = defineProps({
  node: Object,
  nodes: Array,
  expandedNodes: {
    type: Object,
    default: () => new Set(),
  },
});

const childNodes = computed(() =>
  props.nodes.filter((n) => n.parent_id === props.node.id)
);

const isExpanded = computed(
  () => hasChildren.value && props.expandedNodes.has(props.node.id)
);
const hasChildren = computed(() => !!childNodes.value.length);
const iconClass = computed(
  () =>
    `mdi mdi-${
      isExpanded.value
        ? "chevron-down"
        : hasChildren.value
        ? "chevron-right"
        : "minus"
    }`
);

const toggleExpand = () => {
  if (isExpanded.value) {
    props.expandedNodes.delete(props.node.id);
  } else {
    props.expandedNodes.add(props.node.id);
  }
};

const level = computed(() => {
  let parent = props.node;
  let level = 0;
  while (parent.parent_id !== null) {
    level++;
    parent = props.nodes.find((node) => node.id === parent.parent_id);
  }
  return level;
});
</script>
