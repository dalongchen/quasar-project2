<template>
  <q-btn :label="flatLable" @click="promptNow" />
  <q-dialog v-model="prompt">
    <q-card class="my-card">
      <q-form @submit="onSubmit" @reset="onReset">
        <q-date v-model="date" landscape minimal :options="optionsFn" />
        <div class="row" style="max-width: 50rem;">
          <div class="col-auto">
            <q-select label="复权" filled v-model="fq" :options="opt" dense options-dense />
          </div>
          <div class="col">
            <q-input dense filled type="number" v-model="day_num" label="连续年数" lazy-rules
              :rules="[val => val !== null && val !== '' || 'Please type ', val => val > 0 && val < 5 || 'Please type']" />
          </div>
          <div class="col">
            <q-input dense filled type="number" v-model="up_num" label="净资产收益率" lazy-rules
              :rules="[val => val !== null && val !== '' || 'Please type ', val => val > 0 && val < 100 || 'Please type']" />
          </div>
          <div class="col-2">
            <q-input dense filled type="number" v-model="down_num" label="负幅度" lazy-rules
              :rules="[val => val !== null && val !== '' || 'Please type ', val => val > -100 && val < 2 || 'Please type']" />
          </div>
        </div>
        <q-btn dense :label="flatLable" type="submit" flat class="q-ml-xs" />
        <q-btn dense label="Reset" type="reset" flat class="q-ml-xs" />
      </q-form>
    </q-card>
  </q-dialog>
</template>

<script>
import { ref } from 'vue'
import { api } from 'boot/axios'

export default {
  name: 'TableZhangTing',
  data() {
    return {
      date: '89',
      prompt: false,
      dateNow: '',
      fq: '未选',
      opt: [
        '更新交易股票',
        'baostock历史k',
        'east历史k',
        '不复权',
        '后复权',
        '不/后复',
      ]
    };
  },
  props: {
    flatLable: {
      type: String,
      default: 'yut'
    },
    link: {
      type: String,
      default: '/polls/zhangTing/'
    },
    nameList: {
      type: Function,
    },
  },
  methods: {
    promptNow() {
      this.prompt = true
      // console.log(this.prompt, 'uu2u')
      let now = new Date()
      const month = now.getMonth() >= 9 ? now.getMonth() + 1 : `0${now.getMonth() + 1}`
      const date3 = now.getDate() >= 10 ? now.getDate() : `0${now.getDate()}`
      this.dateNow = `${now.getFullYear()}/${month}/${date3}`
      this.date = this.dateNow
      // console.log(this.dateNow)
    },
    onSubmit() {
      // console.log(this.fq !== '' && this.date !== '89', 'uu2u', this.fq, this.date)
      if (this.fq !== '未选' && this.date !== '89') {
        api.get(this.link, {
          params: {
            quarter: this.date,
            fq: this.fq,
          },
        }).then(res => {
          // console.log(res)
          this.nameList(res);
        }).catch((err) => {
          console.log(err);
        });
      }
    },
    optionsFn(date1) {
      return date1 <= this.dateNow
    },
  },
  setup() {
    const day_num = ref(1)
    const up_num = ref(19)
    const down_num = ref(-5)

    function onReset() {
      // date.value = null
      day_num.value = null
      up_num.value = null
      down_num.value = null
    }

    return {
      onReset,
      day_num,
      up_num,
      down_num,
    }
  }
}
</script>
<style lang="sass" scoped>
.my-card
  width: 100%
  max-width: 420px
</style>