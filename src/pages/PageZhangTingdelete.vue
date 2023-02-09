<template>
  <!-- <KeepAlive> -->
  <TableZhangTing></TableZhangTing>
  <!-- <TableStock :data="data"></TableStock> -->
  <!-- </KeepAlive> -->
</template>

<script>
import TableZhangTing from 'components/TableZhangTing.vue';
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageZhangTing',
  components: { TableZhangTing },
  // setup() {
  //   // let data = reactive({ col: [], da: [], code2: [], name2: [], names: [] });
  //   return {
  //   };
  // }
});
</script>
