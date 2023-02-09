<template>
  <!-- xs=4 md中,sm=8所有方向小的padding -->
  <div class="q-pa-xs">
    <q-table class="my-sticky-header-table" title="Treats" :rows="rows" :columns="columns" row-key="name" flat bordered
      dense :rows-per-page-options="[20, 50, 100, 200, 500, 1000, 0]" rows-per-page-label=" "
      :visible-columns="visibleColumns" :filter="filter">
      <template v-slot:top-right>
        <q-btn label="search" @click="prompt = true" />
        <q-dialog v-model="prompt">
          <q-card>
            <q-form @submit="onSubmit" @reset="onReset">
              <q-select label="季度" filled v-model="quarter" :options="opt" dense options-dense multiple />

              <div class="row" style="max-width: 50rem;">
                <div class="col-2">
                  <q-input dense filled type="number" v-model="day_num" label="连续年数" lazy-rules
                    :rules="[val => val !== null && val !== '' || 'Please type ', val => val > 0 && val < 5 || 'Please type']" />
                </div>
                <div class="col-2">
                  <q-input dense filled type="number" v-model="up_num" label="净资产收益率" lazy-rules
                    :rules="[val => val !== null && val !== '' || 'Please type ', val => val > 0 && val < 100 || 'Please type']" />
                </div>
                <div class="col-2">
                  <q-input dense filled type="number" v-model="down_num" label="负幅度" lazy-rules
                    :rules="[val => val !== null && val !== '' || 'Please type ', val => val > -100 && val < 2 || 'Please type']" />
                </div>
              </div>
              <q-btn dense label="Submit" type="submit" flat class="q-ml-xs" />
              <q-btn dense label="Reset" type="reset" flat class="q-ml-xs" />
            </q-form>
          </q-card>
        </q-dialog>
        <q-select v-model="visibleColumns" multiple outlined dense options-dense :display-value="$q.lang.table.columns"
          emit-value map-options :options="columns" option-value="name" options-cover style="min-width: 150px" />
        <q-input borderless dense debounce="300" v-model="filter" placeholder="Search">
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>
      </template>

      <template v-slot:body-cell-0="props">
        <q-td :props="props">
          <a target="_blank" :href="'#/stockStandardk/' + props.value + '/code2/' + code2 + '/name2/' + name2">
            {{ props.value }}
          </a>
        </q-td>
      </template>

    </q-table>
  </div>
</template>

<script>
import { reactive, ref } from 'vue'
// import { toRef, ref } from 'vue'
import { api } from 'boot/axios'

export default {
  name: 'TableStockYjbbEm',
  // props: {
  //   data: {
  //     type: Object
  //   },
  // },
  // setup(props) {
  setup() {
    //   let rows = toRef(props.data, 'da')
    let rows = reactive([])
    let columns = reactive([])
    let code2 = reactive([])
    let name2 = reactive([])
    let visibleColumns = reactive([])
    let opt = [
      '20211231',
      '20201231',
      '20191231',
      'all'
    ]

    // const quarter = ref('2020年1-3,2020年1-6')
    const quarter = ref(['20211231'])
    const day_num = ref(1)
    const up_num = ref(19)
    const down_num = ref(-5)
    function onSubmit() {
      if (quarter.value[0] == 'all') {
        quarter.value = opt.slice(0, opt.length - 1)
      }
      api.get('/polls/stockYjbbEm/', {
        params: {
          quarter: quarter,
          // quarter: quarter.value,
          day_num: day_num.value,
          up_num: up_num.value,
          down_num: down_num.value,
        },
      }).then(res => {
        rows.length = 0
        columns.length = 0
        code2.length = 0
        name2.length = 0
        visibleColumns.length = 0
        // console.log(rows.length)
        rows.push(...res.data.da)
        columns.push(...res.data.col)
        code2.push(...res.data.code2)
        name2.push(...res.data.name2)
        visibleColumns.push(...res.data.col.map(function (user) { return user.name; }))
        // console.log(code2.length)
      }).catch((err) => {
        console.log(err);
      });
    };
    // onSubmit();
    function onReset() {
      quarter.value = null
      day_num.value = null
      up_num.value = null
      down_num.value = null
    }
    return {
      rows,
      columns,
      code2,
      name2,
      visibleColumns,
      filter: ref(''),

      prompt: ref(false),
      opt,
      onSubmit,
      onReset,
      quarter,
      day_num,
      up_num,
      down_num,
    }
  }
}
</script>
<style lang="sass">
.my-sticky-header-table
  /* height or max-height is important */
  height: 33.5rem

  .q-table__top,
  .q-table__bottom,
  thead tr:first-child th
    /* bg color is important for th; just specify one */
    background-color: #c1f4cd

  thead tr th
    position: sticky
    z-index: 1
  thead tr:first-child th
    top: 0

  /* this is when the loading indicator appears */
  &.q-table--loading thead tr:last-child th
    /* height of all previous header rows */
    top: 48px
</style>
<style type="text/css">
a {
  /* 去除默认的下划线 */
  text-decoration: none;
  /* 去除默认的颜色和点击后变化的颜色 */
  color: #000;
}
</style>