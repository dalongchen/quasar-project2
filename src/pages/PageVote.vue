<template>
  <!-- <KeepAlive> -->
  <TableStock></TableStock>
  <!-- <TableStock :data="data"></TableStock> -->
  <!-- </KeepAlive> -->
</template>

<script>
import TableStock from 'components/TableStock.vue';
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageVote',
  components: { TableStock },
  // setup() {
  //   // let data = reactive({ col: [], da: [], code2: [], name2: [], names: [] });
  //   return {
  //   };
  // }
});
</script>
