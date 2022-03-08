<template>
  <div class="main-content-container container-fluid px-4">
    <!-- Page Header -->
    <div class="page-header row no-gutters py-4">
      <div class="col-12 col-sm-4 text-center text-sm-left mb-0">
        <span class="text-uppercase page-subtitle">概览</span>
        <h3 class="page-title">设置</h3>
      </div>
    </div>

    <!-- Default Light Table -->
    <div class="row" v-for="(settings, section) in config" :key="section">
      <div class="col">
        <div class="card card-small mb-4">
          <div class="card-header border-bottom">
            <h6 class="m-0">{{ dict[section] }}</h6>
          </div>
          <div class="card-body p-0 pb-3">
            <d-list-group flush>
            <d-list-group-item class="p-3" v-for="(value, name) in settings" :key="name">
            <d-row>
              <d-col sm="12" md="2">
               <label>{{ dict[name] }}</label>
              </d-col>
              <d-col sm="12" md="10">
                <d-input v-if="value == null" type="text" :placeholder="'missing `' + name + '` in configuration file'" state="invalid" readonly/>
                <d-input v-else-if="value.constructor == Object || value.constructor == Array" type="text" :value="JSON.stringify(value)" readonly/>
                <d-input v-else type="text" :value="value.toString()" readonly/>
              </d-col>
            </d-row>
            </d-list-group-item>
            </d-list-group>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
const axios = require('axios');

export default {
  data() {
    return {
      config: null,
      dict: {
            "Database": "数据库",
              "DataStore": "数据存储",
              "CacheStore": "",
              "AutoInsertUser": "",
              "AutoInsertItem": "",
              "CacheSize": "",
              "PositiveFeedbackType": "",
              "ReadFeedbackTypes": "",
              "PositiveFeedbackTTL": "",
              "ItemTTL": "",
            "Master": "主节点",
              "Port": "",
              "Host": "",
              "HttpPort": "",
              "HttpHost": "",
              "NumJobs": "",
              "MetaTimeout": "",
              "DashboardUserName": "",
              "DashboardPassword": "",
            "Server": "服务器节点",
              "APIKey": "",
              "DefaultN": "",
            "Recommend": "推荐",
              "PopularWindow": "",
              "FitPeriod": "",
              "SearchPeriod": "",
              "SearchEpoch": "",
              "SearchTrials": "",
              "CheckRecommendPeriod": "",
              "RefreshRecommendPeriod": "",
              "FallbackRecommend": "",
              "NumFeedbackFallbackItemBased": "",
              "ExploreRecommend": "",
              "EnableItemNeighborIndex": "",
              "ItemNeighborType": "",
              "ItemNeighborIndexRecall": "",
              "ItemNeighborIndexFitEpoch": "",
              "EnableUserNeighborIndex": "",
              "UserNeighborType": "",
              "UserNeighborIndexRecall": "",
              "UserNeighborIndexFitEpoch": "",
              "EnableLatestRecommend": "",
              "EnablePopularRecommend": "",
              "EnableUserBasedRecommend": "",
              "EnableItemBasedRecommend": "",
              "EnableColRecommend": "",
              "EnableColIndex": "",
              "ColIndexRecall": "",
              "ColIndexFitEpoch": "",
              "EnableClickThroughPrediction": "",
              "EnableReplacement": "",
              "PositiveReplacementDecay": "",
              "ReadReplacementDecay": "",
      },
    };
  },
  mounted() {
    axios.get('/api/dashboard/config')
      .then((response) => {
        this.config = response.data;
      });
  },
};
</script>
