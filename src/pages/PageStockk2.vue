<template>
  <q-carousel v-model="slide" vertical transition-prev="slide-down" transition-next="slide-up" swipeable animated
    control-color="white" navigation-icon="radio_button_unchecked" padding arrows height="32rem"
    class="text-white shadow-1 rounded-borders" keep-alive infinite>
    <q-carousel-slide :name="data" class="column no-wrap flex-center" v-for='(data, index ) in code2' :key="index">
      {{ name2[index] }}{{ data }}
      <LearnChartk :mydata="data"></LearnChartk>
    </q-carousel-slide>
  </q-carousel>
</template>

<script>
import LearnChartk from 'src/components/LearnChartk.vue';
import { defineComponent } from 'vue';
import { useRoute } from 'vue-router';

export default defineComponent({
  name: 'PageStockk2',
  components: { LearnChartk },
  data() {
    return {
      code2: [],
      name2: [],
      virtualListIndex: 2,
      slide: 0
    }
  },
  mounted() {
    let mydata = useRoute().params.id
    // console.log(mydata)
    this.slide = mydata
    this.code2 = useRoute().params.code2.split(',')
    this.name2 = useRoute().params.name2.split(',')
    // Object.freeze(this.heavyList)
  },
  // setup() {
  //   return {
  //   }
  // }
})
</script>