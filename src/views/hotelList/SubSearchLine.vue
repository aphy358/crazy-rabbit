
<!-- 关键字搜索栏 -->
<template>
    <div class="sub-search-line-wrap">
        <span>共搜索到<span class="ssl-hotel-num">{{getPageRecordCount}}</span>家酒店符合条件</span>
        <div class="ssl-search-wrap">
            <el-input placeholder="请输入关键词" size="small" clearable style="width:350px" 
              v-model="keywords"
              @input="setKeywords"
              @keydown.enter.native="setKeywords">
              <el-button slot="append" icon="el-icon-search" @click="setKeywords"></el-button>
            </el-input>
        </div>
    </div>
</template>

<script>
export default {
  name: '',

  data(){
    return {
      keywords: '',
    }
  },

  computed: {
    getPageRecordCount(){
      return this.$store.state.hotelList.pageRecordCount
    }
  },
  
  methods: {
    setKeywords($event){
      let payload = { t: "keywords", v: this.keywords }
      if(typeof $event !== 'string'){
        payload.api = this.$api
      }

      this.$store.dispatch("hotelList/actionHotelList", payload)
    }
  }
}
</script>

<style lang="scss">
.sub-search-line-wrap{
  .el-icon-search{
    font-size: 16px;
  }

  .el-input-group--append .el-input__inner,
  .el-input-group__prepend{
    border-radius: 0;
  }

  .el-input-group--prepend .el-input__inner,
  .el-input-group__append{
    border-radius: 0;
  }
}
</style>

<style scoped lang="scss">
@import "../../assets/jl_sprites.scss";

.sub-search-line-wrap{
    font-size: 14px;
    width: 1200px;
    height: 50px;
    line-height: 30px;
    background: white;
    margin: 10px auto;
    padding: 10px 20px;
    box-sizing: border-box;
    box-shadow: 0 0 5px #dadada;
    font-size: 14px;

    @at-root .ssl-hotel-num{
        font-size: 20px;
        color: #fea925;
        margin: 0 2px;
    }

    @at-root .ssl-search-wrap{
        float: right;
    }
}
</style>