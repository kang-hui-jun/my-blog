# 个人博客
<template>
  <mescroll-uni
    @init="mescrollInit"
    @down="refreshChange"
    @up="loadChange"
    v-bind="$attrs"
  >
    <slot></slot>
  </mescroll-uni>
</template>

<script lang="ts">
import { Vue, Component, Prop } from "vue-property-decorator";

@Component
export default class Paging extends Vue {
  @Prop() value!: any[];

  page = 1;
  size = 10;
  mescroll: any = null;
  mescrollInit(mescroll) {
    this.mescroll = mescroll;
  }
  refreshChange() {
    this.mescroll.removeEmpty();
    this.mescroll.showDownScroll();
    this.page = 1;
    this.query();
  }
  complete(data) {
    const isEmpty = (list) => {
      if (!list || !list.length) {
        this.mescroll.endUpScroll(false);
        this.mescroll.endDownScroll();
        this.mescroll.showEmpty();
      } else {
        this.mescroll.endSuccess(20, Boolean(list.length));
      }
    };
    let result = [];
    if (this.page === 1) {
      result = data;
      isEmpty(result);
    } else {
      if (data.length) {
        result = [...this.value, ...data];
        isEmpty(result);
      } else {
        isEmpty(data);
      }
    }
    this.$emit("input", result);
  }
  loadChange() {
    this.page++;
    this.query();
  }
  query() {
    this.$emit("query", this.page, this.size);
  }
  reload() {
    this.$nextTick(() => {
      this.refreshChange();
    });
  }
  created() {
    this.reload();
  }
}
</script>
