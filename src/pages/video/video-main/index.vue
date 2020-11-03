<template>
	<scroll-view 
  scroll-y 
  enable-flex 
  class="vide_main"
  @scrolltolower="handleScrolltolower">
    <view class="view_item"
    v-for="item in videowp"
    :key="item.id"
    @click="handleVideo(item)">
      <image :src="item.img" mode="widthFix" />
    </view>
	</scroll-view>
</template>

<script>
export default{
  props:{
    urlobj:Object
  },
  data(){
    return{
      videowp:[],
      hasMore:true
    }
  },
  watch:{
    urlobj(){
      this.videowp=[];
      this.getList();
      // console.log("参数变化");
    }
  },
  mounted(){
    // console.log(this.urlobj);
    this.getList()
  },
  methods:{
    getList(){
      this.request({
        url:this.urlobj.url,
        data:this.urlobj.params
      })
      .then(result=>{
        if (result.res.videowp.length===0) {
          this.hasMore=false;
          uni.showToast({
            title: '没有更多数据了',
            icon: "none"
          });
          return;
        }
        this.videowp=[...this.videowp,...result.res.videowp];
        // console.log(result);
      })
    },
    handleScrolltolower(){
      if(this.hasMore){
        this.urlobj.params.skip+=this.urlobj.params.limit;
        this.getList();
      }else{
        uni.showToast({
          title: '没有更多数据了',
          icon: "none"
        });
      }
    },
    handleVideo(item){
      getApp().globalData.video=item;
      uni.navigateTo({
        url: '/pages/videoPlay/index'
      });
    }
  }
}
</script>

<style lang='scss' scoped>
.vide_main{
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .view_item{
    width: 33.33%;
    border: 5rpx solid #fff;
    image{}
  }
}
</style>
