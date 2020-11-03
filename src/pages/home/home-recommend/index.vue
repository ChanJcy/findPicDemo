<template>
	<scroll-view @scrolltolower="handleToLower" scroll-y class="recommend_view" v-if="recommends.length>0">
    <!-- 推荐开始 -->
    <view class="recommend_wrap">
      <navigator class="recommend_item"
      v-for="item in recommends"
      :key="item.id"
      :url="`/pages/album/index?id=${item.target}`">
      <image 
      mode="widthFix"
      :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐结束 -->
    <!-- 月份开始 -->
    <view class="monthes_wrap">
      <view class="monthes_title">
        <view class="monthes_title_info">
          <view class="monthes_info">
            <text>{{monthes.DD}} / </text>
              {{monthes.MM}}月
          </view>
          <view class="monthes_text">你负责美丽就好</view>
        </view>
        <view class="monthes_title_more">更多></view>
      </view>
      <view class="monthes_content">
        <view class="monthes_item"
        v-for="(item,index) in monthes.items"
        :key="item.id">
          <go-details :list="monthes.items" :index="index">
            <image mode="widthFix" :src="item.thumb+item.rule.replace('$<Height>',300)"></image>
          </go-details>
        </view>
      </view>
    </view>
    <!-- 月份结束 -->
    <!-- 热门开始 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hots_item"
        v-for="(item,index) in hots"
        :key="item.id">
          <go-details :list="hots" :index="index">
            <image :src="item.thumb" mode="widthFix"></image>
          </go-details>
        </view>
      </view>
    </view>
    <!-- 热门结束 -->
	</scroll-view>
</template>

<script>
import moment from 'moment';
import goDetails from '@/components/goDetails'
export default{
  components:{
    goDetails
  },
  data(){
    return{
      recommends:[],
      monthes:{},
      hots:[],
      params:{
        limit:30,
        order:'hot',
        skip:0
      },
      hasMore:true
    }
  },
  mounted(){
    this.getList();
  },
  methods:{
    getList(){
      this.request({
      url:'http://157.122.54.189:9088/image/v3/homepage/vertical',
      data:this.params
    }).then( result =>{
      if(result.res.vertical.length === 0){
        this.hasMore=false;
        uni.showToast({
          title: '没有更多数据了！',
          icon: "none"
        });
        return;
      }
      if(this.recommends.length === 0){
        this.recommends = result.res.homepage[1].items;
        this.monthes = result.res.homepage[2];
        this.monthes.MM=moment(this.monthes.stime).format('MM');
        this.monthes.DD=moment(this.monthes.stime).format('DD');
      }
      this.hots=[...this.hots,...result.res.vertical];
      // console.log(this.hots);
      

    })
    },
    handleToLower(){
      if(this.hasMore){
        this.params.skip+=this.params.limit;
        this.getList();
      }else{
        uni.showToast({
          title: '没有数据啦！',
          icon:"none"
        });
      }
    }
  }
}
</script>

<style lang='scss' scoped>
.recommend_view{
  height:calc(100vh - 36px);
}
.recommend_wrap{
  display: flex;
  flex-wrap: wrap;
  .recommend_item{
    width: 50%;
    border: 5rpx solid #fff;
  }
}
.monthes_wrap{
  .monthes_title{
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .monthes_title_info{
      color:$color;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .monthes_info{
        text{
          font-size: 36rpx;
        }
      }
      .monthes_text{
        font-size: 34rpx;
        color: #666;
        margin-left: 30rpx;
      }
    }
    .monthes_title_more{
      font-size: 34rpx;
      color:$color;
    }
  }
  .monthes_content{
    display: flex;
    flex-wrap: wrap;
    .monthes_item{
      width:33.3%;
      border: 5rpx solid #fff;
    }
  }
}
.hots_wrap{
  .hots_title{
    padding: 20rpx;
    text{
      font-size: 30rpx;
      padding-left: 20rpx;
      font-weight: 600;
      color: $color;
      border-left: 20rpx solid $color;
    }
  }
  .hots_content{
    display: flex;
    flex-wrap: wrap;
    .hots_item{
      width: 33.3%;
      border: 5rpx solid #fff;
      image{}
    }
  }
}
</style>