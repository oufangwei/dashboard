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
              <d-col sm="12" md="5">
               <label>{{ dict[name] }}</label>
              </d-col>
              <d-col sm="12" md="7">
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
              "CacheStore": "缓存存储",
              "AutoInsertUser": "自动插入用户",
              "AutoInsertItem": "自动插入问题/活动",
              "CacheSize": "缓存大小",
              "PositiveFeedbackType": "正向反馈类型",
              "ReadFeedbackTypes": "阅读反馈类型",
              "PositiveFeedbackTTL": "正向反馈生存时间",
              "ItemTTL": "问题/活动生存时间",
            "Master": "主节点",
              "Port": "端口",
              "Host": "主机",
              "HttpPort": "HTTP端口",
              "HttpHost": "HTTP主机",
              "NumJobs": "工作数量",
              "MetaTimeout": "元超时",
              "DashboardUserName": "智能推荐系统用户名",
              "DashboardPassword": "智能推荐系统密码",
            "Server": "服务器节点",
              "APIKey": "API密钥",
              "DefaultN": "默认返回元素数量",
            "Recommend": "推荐",
              "PopularWindow": "热门问题/活动时间窗口大小",
              "FitPeriod": "训练周期",
              "SearchPeriod": "搜索周期",
              "SearchEpoch": "模型搜索训练次数",
              "SearchTrials": "模型搜索试验次数",
              "CheckRecommendPeriod": "用户推荐检查周期",
              "RefreshRecommendPeriod": "离线推荐缓存刷新周期",
              "FallbackRecommend": "个性化推荐耗尽后的推荐来源",
              "NumFeedbackFallbackItemBased": "问题/活动相似推荐的反馈返回数量",
              "ExploreRecommend": "探索推荐方法",
              "EnableItemNeighborIndex": "启用向量索引进行近似问题/活动邻居搜索",
              "ItemNeighborType": "问题/活动邻居类型",
              "ItemNeighborIndexRecall": "近似问题/活动邻居搜索的最小召回率",
              "ItemNeighborIndexFitEpoch": "近似问题/活动邻居搜索的最大训练次数",
              "EnableUserNeighborIndex": "启用向量索引进行近似用户邻居搜索",
              "UserNeighborType": "用户邻居类型",
              "UserNeighborIndexRecall": "近似用户邻居搜索的最小召回率",
              "UserNeighborIndexFitEpoch": "近似用户邻居搜索的最大训练次数",
              "EnableLatestRecommend": "在离线推荐时启用最新推荐",
              "EnablePopularRecommend": "在离线推荐时启用热门推荐",
              "EnableUserBasedRecommend": "在离线推荐时启用基于用户的相似度推荐",
              "EnableItemBasedRecommend": "在离线推荐时启用基于问题/活动的相似度推荐",
              "EnableColRecommend": "在离线推荐时启用协同过滤推荐",
              "EnableColIndex": "启用向量索引进行近似协同过滤推荐",
              "ColIndexRecall": "近似协同过滤推荐的最小召回率",
              "ColIndexFitEpoch": "近似协同过滤推荐的最大训练次数",
              "EnableClickThroughPrediction": "在离线推荐期间启用点击率预测",
              "EnableReplacement": "将历史问题/活动替换回推荐",
              "PositiveReplacementDecay": "衰减正向反馈中的替换权重",
              "ReadReplacementDecay": "衰减读取反馈中的替换权重",
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
