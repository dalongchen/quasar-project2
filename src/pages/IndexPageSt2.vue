<template>
  <!-- <q-page class=""> -->
  <!-- <div class="row q-col-gutter-sm q-ml-xs q-mr-sm q-py-sm"> -->
  <swiper :modules="modules" :slides-per-view="1" mousewheel :initial-slide='1'
    @slideNextTransitionStart="onSlideChange" :pagination="{ type: 'fraction' }" :lazy="{ loadPrevNext: true }">
    <swiper-slide v-for="todo in arr2" :key="todo">{{ todo }}
      <LearnChartk :mydata="todo" />
    </swiper-slide>
    <!-- <swiper-slide>
        <LearnChartk></LearnChartk>
      </swiper-slide> -->
  </swiper>
  <!-- </div> -->
  <!-- </q-page> -->
</template>

<script>
import LearnChartk from 'src/components/LearnChartk.vue';
import { defineComponent, reactive } from 'vue';
// import { defineComponent, onMounted, reactive, toRefs } from 'vue';
import { Swiper, SwiperSlide } from 'swiper/vue';
import { Mousewheel, Manipulation, Pagination, Lazy } from 'swiper';
import 'swiper/css';
import 'swiper/css/mousewheel';
import 'swiper/css/manipulation';
import 'swiper/css/pagination';
import 'swiper/css/lazy';
import { useRoute } from 'vue-router';


export default defineComponent({
  name: 'IndexPageSt2',
  components: { LearnChartk, Swiper, SwiperSlide, },
  setup() {
    let mydata = useRoute().params.id
    let postId = useRoute().params.postId
    let arr = postId.split(',')
    var index = arr.findIndex((item) => {
      return item === mydata
    })
    let arr2 = reactive(arr.slice(index - 1, index + 2))
    // console.log(index)
    // console.log(Object.prototype.toString.call(postId))
    // setInterval(() => {
    //   mydata.dat.value += '='
    // }, 2000)
    // const onSwiper = () => {
    //   // const onSwiper = (swiper) => {
    //   console.log('');
    //   // console.log(swiper);
    // };
    const onSlideChange = () => {
      // swiper.appendSlide('<div class="swiper-slide">w<LearnChartk/></div>')
      // swiper.update()
      // console.log(swiper.isBeginning, swiper.isEnd);
      // if (re.activeIndex === 2) {
      //   arr2 = arr.slice(index + 2, index + 5)
      //   console.log('uu', arr2);
      // }
    };
    return {
      onSlideChange,
      modules: [Mousewheel, Manipulation, Pagination, Lazy],
      arr2
    };
  },
})
</script>