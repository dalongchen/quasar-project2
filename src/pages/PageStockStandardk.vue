<template>
  <q-carousel v-model="slide" vertical transition-prev="slide-down" transition-next="slide-up" swipeable animated
    control-color="white" navigation-icon="radio_button_unchecked" padding height="32rem" ref="carousel"
    v-model:fullscreen="fullscreen" class="text-white shadow-1 rounded-borders" keep-alive infinite
    :autoplay="autoplay">
    <q-carousel-slide :name="data2" class="column no-wrap flex-center" v-for='(data2, index) in code2' :key="index">
      <StockStandardk :mydata="data2"></StockStandardk>
      <q-carousel-control position="top-right" :offset="[0, 0]" class="q-gutter-xs">
        <q-item clickable tag="a" target="_blank" :href="publishedMessage(data2)" dense>
          {{ name2[index] }}{{ data2 }}
        </q-item>
      </q-carousel-control>
    </q-carousel-slide>
    <template v-slot:control>
      <q-carousel-control position="bottom-left" :offset="[5, 5]" class="text-white rounded-borders"
        style="background: rgba(0, 0, 0, .3); padding: 4px 8px;">
        <q-toggle dense dark color="orange" v-model="autoplay" label="Auto" />
        <q-btn push round dense color="white" text-color="primary" :icon="fullscreen ? 'fullscreen_exit' : 'fullscreen'"
          @click="fullscreen = !fullscreen" />
      </q-carousel-control>

      <q-carousel-control position="bottom-right" :offset="[18, 18]" class="q-gutter-xs">
        <q-btn push round dense color="orange" text-color="black" icon="arrow_left"
          @click="$refs.carousel.previous()" />
        <q-btn push round dense color="orange" text-color="black" icon="arrow_right" @click="$refs.carousel.next()" />
      </q-carousel-control>
    </template>
  </q-carousel>
</template>

<script>
import StockStandardk from 'src/components/StockStandardk.vue';
import { defineComponent } from 'vue';
import { useRoute } from 'vue-router';

export default defineComponent({
  name: 'PageStockStandardk',
  components: { StockStandardk },
  data() {
    return {
      code2: [],
      name2: [],
      virtualListIndex: 2,
      slide: 0,
      autoplay: false,
      fullscreen: false,
      // autoplay: ref(false),
      // fullscreen: ref(false),
    }
  },
  mounted() {
    // let mydata = useRoute().params.id
    // console.log(mydata)
    this.slide = useRoute().params.id
    this.code2 = useRoute().params.code2.split(',')
    this.name2 = useRoute().params.name2.split(',')
    // Object.freeze(this.heavyList)
  },
  methods: {
    publishedMessage(dat) {
      if (dat.substr(0, 1) == '6') {
        dat = 'sh' + dat
      } else {
        dat = 'sz' + dat
      }
      return 'https://emweb.securities.eastmoney.com/OperationsRequired/Index?type=web&code=' + dat
      // return 'https://emweb.securities.eastmoney.com/NewFinanceAnalysis/Index?type=web&code=' + dat
    }
  }
  // setup() {
  //   return {
  //   }
  // }
})
</script>