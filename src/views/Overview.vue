<template>
  <d-container fluid class="main-content-container px-4">
    <!-- Page Header -->
    <d-row no-gutters class="page-header py-4">
      <d-col col sm="4" class="text-center text-sm-left mb-4 mb-sm-0">
        <span class="text-uppercase page-subtitle">智能推荐系统</span>
        <h3 class="page-title">概览</h3>
      </d-col>
    </d-row>

    <!-- Small Stats Blocks -->
    <d-row>
      <d-col lg v-for="(stats, idx) in smallStats" :key="idx" class="mb-4">
        <small-stats :id="`small-stats-${idx}`" variation="1" :chart-data="stats.datasets" :label="stats.label" :value="stats.value" :tip="stats.tip" :increase="stats.increase" :decrease="stats.decrease" />
      </d-col>
    </d-row>

    <d-row>
      <!-- Users Overview -->
      <d-col lg="12" md="12" sm="12" class="mb-4">
        <bo-users-overview />
      </d-col>
    </d-row>

    <d-row>
      <d-col lg="7" md="12" sm="12" class="mb-4">
        <d-tabs>
          <d-tab title="热门问题/活动" active>
            <bo-top-items :api="'/api/dashboard/popular/'" />
          </d-tab>
          <d-tab title="最新问题/活动">
            <bo-top-items :api="'/api/dashboard/latest/'" />
          </d-tab>
        </d-tabs>
      </d-col>

      <d-col lg="5" md="12" sm="12" class="mb-4">
        <bo-users-by-device />
      </d-col>
    </d-row>
  </d-container>
</template>

<script>
import SmallStats from '@/components/common/SmallStats.vue';
import CategorizedItems from '@/components/common/CategorizedItems.vue';
import UsersOverview from '@/components/statistics/UsersOverview.vue';
import UsersByDevice from '@/components/statistics/UsersByDeviceLite.vue';

const axios = require('axios');
const numeral = require('numeral');

export default {
  components: {
    SmallStats,
    boUsersOverview: UsersOverview,
    boUsersByDevice: UsersByDevice,
    boTopItems: CategorizedItems,
  },
  data() {
    return {
      cacheSize: 100,
      smallStats:
       [{
         label: '用户',
         value: '--',
         tip: '',
       }, {
         label: '问题/活动',
         value: '--',
         tip: '',
       }, {
         label: '全部正向反馈',
         value: '--',
         tip: '',
       }, {
         label: '有效正向反馈',
         value: '--',
         tip: '只有当该用户同时拥有正面反馈和负面反馈时，正面反馈才有效',
       }, {
         label: '有效负向反馈',
         value: '--',
         tip: '只有当该用户同时拥有正面反馈和负面反馈时，负面反馈才有效',
       }],
      popularItems: [],
      latestItems: [],
    };
  },
  mounted() {
    // load config
    axios.get('/api/dashboard/config')
      .then((response) => {
        this.cacheSize = response.data.Database.CacheSize;
      });
    // load status
    axios.get('/api/dashboard/stats')
      .then((response) => {
        this.smallStats[0].value = numeral(response.data.NumUsers).format('0,0');
        this.smallStats[1].value = numeral(response.data.NumItems).format('0,0');
        this.smallStats[2].value = numeral(response.data.NumTotalPosFeedback).format('0,0');
        this.smallStats[3].value = numeral(response.data.NumValidPosFeedback).format('0,0');
        this.smallStats[4].value = numeral(response.data.NumValidNegFeedback).format('0,0');
      });
  },
};
</script>
