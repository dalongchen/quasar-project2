<template>
  <!-- <KeepAlive> -->
  <TableStockYjbbEm></TableStockYjbbEm>
  <!-- <TableStock :data="data"></TableStock> -->
  <!-- </KeepAlive> -->
</template>

<script>
import TableStockYjbbEm from 'components/TableStockYjbbEm.vue';
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageStockYjbbEm',
  components: { TableStockYjbbEm },
  // setup() {
  //   // let data = reactive({ col: [], da: [], code2: [], name2: [], names: [] });
  //   return {
  //   };
  // }
});
</script>
