# 个人博客

<template>
  <view class="test">
    <view class="header">测试</view>
    <mescroll-uni
      ref="mescrollRef"
      :scrollet="true"
      @init="mescrollInit"
      @down="refreshChange"
      @up="loadChange"
      :down="down"
      :up="up"
      :bottom="0"
      :top="88"
      :fixed="true"
    >
      <view class="ul">
        <view class="li" v-for="item in dataList" :key="item.id">
          测试 {{ item.id }}
        </view>
      </view>
    </mescroll-uni>
  </view>
</template>

<script lang="ts">
import { Vue, Component } from "vue-property-decorator";

@Component({})
export default class Test extends Vue {
  params = {
    _page: 1,
    _per_page: 10,
  };
  dataList = [];
  mescroll = null;
  mescrollInit(mescroll) {
    this.mescroll = mescroll;
  }

  down = {
    auto: false,
  };

  up = {
    auto: false,
    textNoMore: "-- 我也是有底线的 --",
    toTop: {
      bottom: 120,
      right: 28,
    },
    onScroll: true,
  };

  refreshChange() {
    this.params._page = 1;
    this.mescroll.removeEmpty();
    this.mescroll.showDownScroll();
    this.getList();
  }

  loadChange() {
    this.params._page++;
    this.getList();
  }

  async getList() {
    try {
      const result = await uni.request({
        url: `http://localhost:3000/posts?_page=${this.params._page}`,
      });

      const { data } = result[1];

      const isMore = Boolean(data.next);
      const isEmpty = (list) => {
        if (!list.length) {
          this.mescroll.endUpScroll(false);
          this.mescroll.endDownScroll();
          this.mescroll.showEmpty();
        } else {
          this.mescroll.endSuccess(20, isMore);
        }
      };

      if (this.params._page === 1) {
        this.dataList = data.data;
        isEmpty(this.dataList);
      } else {
        this.dataList = [...this.dataList, ...data.data];
        isEmpty(this.dataList);
      }
    } catch (error) {
      console.log(error);
    }
  }

  onShow() {
    this.getList();
  }
}
</script>

<style lang="scss">
.header {
  height: 88rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #ccc;
}
</style>
