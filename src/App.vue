<template>
  <div>
    <p>
      <button @click="rerenderTree">Rerender Tree</button>
    </p>
    <transition-group name="fade">
      <div :key="rerenderKey" class>
        <TreeNode
          v-for="node in rootNodes"
          :key="node.id"
          :node="node"
          :nodes="nodes"
          :expanded-nodes="expandedNodes"
        />
      </div>
    </transition-group>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { http } from "./shared/api/http";
import TreeNode from "./shared/components/TreeNode.vue";
import { TNode } from "./shared/types";

const nodes = ref<TNode[]>([]);

const expandedNodes = ref(new Set<string>());

onMounted(async () => {
  const { data } = await http.get("/tree/items");
  nodes.value = data;
});

const rootNodes = computed(() =>
  nodes.value.filter((node) => node.parent_id === null)
);

const rerenderKey = ref(0);
const rerenderTree = () => {
  rerenderKey.value++;
};
</script>
