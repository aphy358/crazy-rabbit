
<!-- 关键字搜索（城市/酒店名） -->
<template>
  <el-popover
    :width="getShowPanel == 1 ? 550 : 390"
    style="width:260px;display: inline-block;"
    :style="extraStyle"
    trigger="focus"
    placement="bottom-start"
    v-model="visible">

    <CitySelectPanel v-if="getShowPanel == 1" @pickvalue="pickvalue($event)" />
    <HotelSelectPanel v-if="getShowPanel == 2" :hotelList="hotelList" :cityList="cityList" @pickvalue="pickvalue($event)" />

    <el-input
      size="small"
      slot="reference"
      placeholder="城市/酒店"
      class="hotel-key-word-select"
      :class="extraClass"
      prefix-icon="el-icon-search"
      v-model="getKeyword"
      @input="queryCityAndHotels"
      @focus="queryCityAndHotels"
      @keydown.enter.native="queryHotelList"
      clearable >
    </el-input>

  </el-popover>
</template>

<script>
import CitySelectPanel from './CitySelectPanel'
import HotelSelectPanel from './HotelSelectPanel'
import { loginFirst } from "../../utils/util.js"


export default {
  name: '',

  data() {
    return {
      visible: false,
      cityList: [],
      hotelList: [],
    }
  },

  // 'page' 是针对不同页面有不同的处理
  props: ['extraStyle', 'page', 'extraClass'],

  components: {
    CitySelectPanel,
    HotelSelectPanel
  },

  computed: {
    // 获取关键字
    getKeyword: {
      get: function () {
        return this.$store.state[this.page].keyword
      },
      set: function (newValue) {
        this.$store.commit(`${this.page}/setCommonState`, {t: 'keyword', v: newValue})

        // 当关键字是通过输入获取的，则只将其当做关键字（所以这里把城市 ID 设置为 null），除非是通过点击某个城市才当做是城市
        this.$store.commit(`${this.page}/setCommonState`, {t: 'cityId', v: null})
      }
    },

    getShowPanel(){
      return this.$store.state[this.page].keyword === '' ? '1' : '2'
    }
  },

  methods: {
    queryCityAndHotels(keyword) {
      keyword = typeof keyword === 'object'
        ? keyword.srcElement.value
        : keyword
      
      if (keyword && keyword !== '') {
        let param = {
          type: this.$store.state.cityType,
          key: keyword,
          keys: keyword
        };

        this.getCityList(param)
        this.getHotelList(param)
      }
    },

    // 查城市
    getCityList(param){
      this.$api.hotelList.syncGetCity(param).then(res => {
        if(res.returnCode === 1 && res.dataList){
          this.cityList = this.setHighlightStr(res.dataList, param.keys, 'aname', 'cityStr')
        }else if(res.errcode == 'notLogin'){
          loginFirst(this)
        }
      })

    },

    // 查酒店列表
    getHotelList(param){
      this.$api.hotelList.syncGetHotels(param).then(res => {
        if(res.returnCode === 1 && res.dataList){
          this.hotelList = this.setHighlightStr(res.dataList, param.keys, 'name', 'hotelStr')
        }else if(res.errcode == 'notLogin'){
          loginFirst(this)
        }
      })

    },

    /**
     * 将关键字搜索返回的结果设置关键字高亮
     * dataList：待处理数组
     * keyword：关键字，如 '深圳'、'上海'
     * name：数组中高亮处理的原始字段名，如 'aname'、'name'
     * strName：高亮处理后新添加的字段名，如 'cityStr'、'hotelStr'
     */
    setHighlightStr(dataList, keyword, name, strName){
      dataList.forEach(n => {
        n.cityName = n.cityName ? this.trim(n.cityName) : ''
        n.provinceName = this.trim(n.provinceName)
        n.countryName = this.trim(n.countryName)
        n[strName] = ''

        for (let i = 0; i < n[name].length; i++) {
          (~keyword.indexOf(n[name][i]))
            ? n[strName] += '<span class="key">' + n[name][i] + '</span>'
            : n[strName] += n[name][i]
        }
      })

      return dataList
    },

    // 去除字符串中所有的大小字母和 '-'
    trim(str){
      return str.replace(/[A-Za-z-]/g, '')
    },

    // 接收子组件发送过来的事件（当选中某个城市或者点击了某个酒店时）
    pickvalue(event){
      this.visible = false
      this.$store.commit(`${this.page}/setCommonState`, {t: 'keyword', v: event.n})
      this.$store.commit(`${this.page}/setCommonState`, {t: 'cityId', v: event.i})
    },

    queryHotelList(){
      this.$store.dispatch(`${this.page}/actionHotelList`, { api: this.$api })
    }
  }
}
</script>

<style lang="scss">
.el-popper[x-placement^=bottom]{
  padding: 0;
}

.search-line-wrap{
  .hotel-key-word-select{
    .el-input__inner{
      padding-left: 30px;
    }
  }
}

.hotel-key-word-select{
  &.no-keyword{
    .el-input__inner{
      background: orangered;
    }
  }
}

</style>
