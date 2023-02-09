<template>
  <!-- xs=4 md中,sm=8所有方向小的padding -->
  <div class="q-pa-xs">
    <!-- <div>gu{{andOr!=='no'}}</div> -->
    <q-table dense :rows="rows" :columns="column" row-key="name" :pagination="initialPagination"
      :rows-per-page-options="[20, 50, 100, 200, 500, 1000, 0]" rows-per-page-label=" " :visible-columns="visibleColumns"
      :filter="filter">
      <template v-slot:top>
        <div class="row">
          <q-select v-model="visibleColumns2" borderless dense options-dense emit-value map-options :options="column"
            option-value="label" options-cover hide-dropdown-icon>
          </q-select>
          <q-select v-model="modelSingle" borderless dense options-dense emit-value map-options :options="options"
            options-cover hide-dropdown-icon>
          </q-select>
          <q-input v-model="inputValue" borderless clearable dense debounce="300" placeholder="?"
            input-style="padding: 0px;width:5vw">
            <template v-slot:append>
              <q-select v-model="visibleColumns23" borderless dense options-dense emit-value map-options
                :options="column2" option-value="label" options-cover hide-dropdown-icon>
              </q-select>
              <q-icon v-if="andOr === 'no'" name="search" v-on:click="getPollStockPreAdd('one')" />
            </template>
          </q-input>
          <q-select v-model="andOr" borderless dense options-dense emit-value map-options :options="optionsAndOr"
            options-cover hide-dropdown-icon>
          </q-select>
          <q-select v-if="andOr !== 'no'" v-model="visibleColumns22" borderless dense options-dense emit-value map-options
            :options="column" option-value="label" options-cover hide-dropdown-icon>
          </q-select>
          <q-select v-if="andOr !== 'no'" v-model="modelSingle2" borderless dense options-dense emit-value map-options
            :options="options" options-cover hide-dropdown-icon>
          </q-select>
          <q-input v-if="andOr !== 'no'" v-model="inputValue2" borderless dense debounce="300" placeholder="?" clearable
            input-style="padding: 0px;width:5vw">
            <template v-slot:append>
              <q-icon name="search" v-on:click="getPollStockPreAdd('two')" />
            </template>
          </q-input>

          <q-input borderless dense debounce="300" v-model="filter" placeholder="过滤"
            input-style="padding: 0px;width:5vw" clearable autogrow>
            <template v-slot:append>
              <q-icon name="search" />
            </template>
          </q-input>
          <q-select v-model="visibleColumns" multiple borderless dense options-dense display-value="列" emit-value
            map-options :options="column" option-value="name" options-cover hide-dropdown-icon>
          </q-select>
        </div>
      </template>

      <template v-slot:body-cell-0="props">
        <q-td :props="props">
          <a target="_blank" :href="'#/stockk/' + props.value + '/posts/' + postId">
            {{ props.value }}
          </a>
        </q-td>
      </template>
      <template v-slot:body-cell-2="props">
        <q-td :props="props">
          <div v-if="props.value !== null" style="font-style:italic;">
            {{ props.value.slice(0, 4) }}
            <q-tooltip :delay="500" :offset="[0, 10]">{{ props.value }}</q-tooltip>
          </div>
        </q-td>
      </template>
      <template v-slot:body-cell-3="props">
        <q-td :props="props">
          <div v-if="props.value !== null" style="font-style:italic;">
            {{ props.value.slice(0, 15) }}
            <q-tooltip :delay="500" :offset="[0, 0]">{{ props.value }}</q-tooltip>
          </div>
        </q-td>
      </template>
      <template v-slot:body-cell-6="props">
        <q-td :props="props">
          <div v-if="props.value !== null" style="font-style:italic;">
            {{ props.value.slice(0, 20) }}
            <q-tooltip :delay="1000" :offset="[0, 10]">{{ props.value }}</q-tooltip>
          </div>
        </q-td>
      </template>
    </q-table>
  </div>
</template>

<script>
import { api } from 'boot/axios'
import { ref } from 'vue'

export default {
  name: 'QTableStock',
  data() {
    return {
      column: [],
      column2: [],
      rows: [],
      modelSingle: ref('like'),
      modelSingle2: ref('like'),
      visibleColumns2: ref('股票代码'),
      visibleColumns22: ref('股票代码'),
      visibleColumns23: ref('重复'),
      inputValue: ref(''),
      inputValue2: ref(''),
      andOr: ref('no'),
      postId: [],
    }
  },
  methods: {
    getPollStockPre() {
      api.get('/polls/').then(res => {
        let rr = res.data.col;
        this.column = rr;
        this.rows = res.data.da;
        this.postId = res.data.code2;
        // console.log(this.postId);
        rr.push({ name: 10, label: '重复', field: 10 });
        this.column2 = rr;
      }).catch((err) => { console.log(err); });
    },
    getPollStockPreAdd(vv) {
      console.log(vv);
      let col;
      if (this.visibleColumns2 === '类型') {
        col = '预告类型'
      } else if (this.visibleColumns2 === '幅度') {
        col = '业绩变动幅度'
      } else {
        col = this.visibleColumns2
      };
      let repace2;
      if (this.visibleColumns23 === '重复') {
        repace2 = ''
      } else {
        repace2 = ' GROUP BY ' + this.visibleColumns23
      }
      let tota1;
      if (this.modelSingle === 'like') {
        tota1 = col + ' ' + this.modelSingle + " '%" + this.inputValue + "%' " + repace2
      } else {
        tota1 = col + this.modelSingle + "'" + this.inputValue + "' " + repace2
      }
      console.log(tota1)

      if (vv === 'two') {
        let col2;
        if (this.visibleColumns22 === '类型') {
          col2 = '预告类型'
        } else if (this.visibleColumns22 === '幅度') {
          col2 = '业绩变动幅度'
        } else {
          col2 = this.visibleColumns22
        };

        if (this.modelSingle2 === 'like') {
          tota1 = col2 + ' ' + this.modelSingle2 + " '%" + this.inputValue2 + "%' " + this.andOr + ' ' + tota1
        } else {
          tota1 = col2 + this.modelSingle2 + "'" + this.inputValue2 + "' " + this.andOr + ' ' + tota1
        }
        console.log(tota1)
      }
      api.get('/polls/5/', {
        params: { tota: tota1 }
      }).then(res => {
        this.rows = res.data.dat;
        console.log(res.data);
      }).catch((err) => {
        console.log(err);
      });
    },
  },
  //加载完成声明周期函数  调用
  mounted() {
    this.getPollStockPre()
  },
  setup() {
    return {
      initialPagination: {
        page: 1,
        rowsPerPage: 20
      },
      visibleColumns: ref([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]),
      options: ['>', '>=', '=', 'like', '!=', '<', '<='],
      optionsAndOr: ['and', 'or', 'no'],
      filter: ref(''),
    }
  }
}
</script>
<style type="text/css">
a {
  /* 去除默认的下划线 */
  text-decoration: none;
  /* 去除默认的颜色和点击后变化的颜色 */
  color: #000;
}
</style>