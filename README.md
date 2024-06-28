# 个人博客
import Vue from 'vue';
import PubSub from './path/to/PubSub'; // 引入上面定义的 PubSub 单例类

// 将 PubSub 的单例实例挂在 Vue 原型上，使得所有 Vue 组件都可以通过 this.$pubsub 访问
Vue.prototype.$pubsub = PubSub.getInstance();

new Vue({
  el: '#app',
  render: h => h(App)
});

## request.js中
import axios from 'axios';
import Vue from 'vue'; // 确保已经引入 Vue

// 发送请求前触发事件
function onRequestStart() {
  Vue.prototype.$pubsub.publish('request-started', { timestamp: new Date() });
}

// 请求成功后触发事件
function onRequestSuccess(response) {
  Vue.prototype.$pubsub.publish('request-success', response.data);
}

// 请求失败后触发事件
function onRequestError(error) {
{
  Vue.prototype.$pubsub.publish('request-error', error);
}

export function get(url) {
  onRequestStart();
  return axios.get(url)
    .then(onRequestSuccess)
    .catch(onRequestError);
}

// 其他请求方法...

## 组件中
<template>
  <div>
    <!-- 页面内容... -->
  </div>
</template>

<script>
export default {
  name: 'Home',
  created() {
    // 订阅请求开始事件
    this.$pubsub.subscribe('request-started', this.onRequestStarted);
    // 订阅请求成功事件
    this.$pubsub.subscribe('request-success', this.onRequestSuccess);
    // 订阅请求错误事件
    this.$pubsub.subscribe('request-error', this.onRequestError);
  },
  beforeDestroy() {
    // 清理订阅，避免内存泄漏
    this.$pubsub.unsubscribe('request-started', this.onRequestStarted);
    this.$pubsub.unsubscribe('request-success', this.onRequestSuccess);
    this.$pubsub.unsubscribe('request-error', this.onRequestError);
  },
  methods: {
    onRequestStarted(data) {
      console.log('Request started at:', data.timestamp);
      // 显示加载指示器等操作
    },
    onRequestSuccess(responseData) {
      console.log('Request successful, received data:', responseData);
      // 更新页面数据等操作
    },
    onRequestError(errorData) {
      console.error('Request failed with error:', errorData);
      // 处理错误信息等操作
    },
    fetchData() {
      // 假设这是调用 request.js 中请求的方法
      this.$api.get('/some-endpoint');
    }
  }
};
</script>
