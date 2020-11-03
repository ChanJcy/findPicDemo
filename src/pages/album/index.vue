<template>
	<view>
    <view class="album_bg">
      <image :src="album.cover" mode="widthFix" />
      <view class="album_info">
        <view class="album_name">{{album.name}}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>
    <!-- 专辑作者开始 -->
    <view class="album_author">
      <view class="album_author_info">
        <image :src="album.user.avatar" mode="widthFix" />
        <view class="author_name">{{album.user.name}}</view>
      </view>
      <view class="album_author_desc">
        <text>{{album.desc}}</text>
      </view>
    </view>
    <!-- 专辑作者结束 -->
    <!-- 列表开始 -->
    <view class="album_list">
      <view class="album_item"
      v-for="(item,index) in wallpaper"
      :key="item.id">
        <go-details :list="wallpaper" :index="index">
          <image :src="item.thumb+item.rule.replace('$<Height>',360)" mode="aspectFill" />
        </go-details>
      </view>
    </view>
    <!-- 列表结束 -->
	</view>
</template>

<script>
import goDetails from '@/components/goDetails'
export default{
  components:{
    goDetails
  },
  data(){
    return{
      params:{
        limit:30,
        order:"new",
        skip:0,
        first:1
      },
      id:-1,
      album:{},
      wallpaper:[],
      hasMore:true
    }
  },
  onLoad(option){
    // console.log(option);
    this.id=option.id
    // this.id='5d5f8e45e7bce75ae7fb8278'
    this.getList()
  },
  onReachBottom(){
    if(this.hasMore){
      this.params.first=0;
      this.params.skip+=this.params.limit;
      this.getList();
    }else{
      uni.showToast({
        title: '没有更多数据了！',
        icon: "none"
      });
    }
  },
  methods:{
    getList(){
      this.request({
        url:`http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data:this.params
      }).then(result=>{
        if(Object.keys(this.album).length===0){
          this.album=result.res.album;
        }
        if(result.res.wallpaper.length===0){
          this.hasMore=false;
          uni.showToast({
            title: '没有更多数据了！',
            icon: "none"
          });
        }
        this.wallpaper=[...this.wallpaper,...result.res.wallpaper];
        // console.log(result);
      })
    }
  }
}
</script>

<style lang='scss' scoped>
.album_bg{
  position:relative;
  image{}
  .album_info{
    position:absolute;
    width:100%;
    left:0;
    bottom:0;
    display: flex;
    justify-content: space-between;
    height: 80rpx;
    align-items:center;
    color: #fff;
    padding: 0 15rpx;
    .album_name{
      font-size: 40rpx;;
    }
    .album_btn{
      background-color: $color;
      width:152rpx;
      height: 60rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}
.album_author{
  padding: 10rpx;
  .album_author_info{
    padding: 10rpx 0;
    display: flex;
    image{
      width:50rpx;
    }
    .author_name{
      color:#000;
      margin-left: 15rpx;
    }
  }
  .album_author_desc{}
}
.album_list{
  display: flex;
  flex-wrap: wrap;
  .album_item{
    width:33.33%;
    border: 3rpx solid #fff;
    image{
      height: 160rpx;
    }
  }
}
</style>