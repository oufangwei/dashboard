<template>
  <d-card class="card-small">
    <!-- Card Header -->
    <d-card-header class="border-bottom">
      <h6 class="m-0">{{ title }}</h6>
      <div class="block-handle"></div>
    </d-card-header>

    <d-card-body class="p-0">
      <!-- Top Referrals List Group -->
      <d-list-group flush class="list-group-small">
        <d-list-group-item
          v-for="(item, idx) in items"
          :key="idx"
          class="d-flex px-3"
        >
          <span class="text-semibold text-fiord-blue"><i class="material-icons mr-1">{{ item.icon }}</i>{{ item.name }}</span>
          <span class="ml-auto text-right text-semibold text-reagent-gray"
            >{{ format(query(item.key)) }}</span
          >
        </d-list-group-item>
      </d-list-group>
    </d-card-body>
    <d-card-footer> </d-card-footer>
  </d-card>
</template>

<script>
import moment from 'moment';

const axios = require('axios');
const jsonPath = require('jsonpath');

export default {
  name: 'users-by-device',
  props: {
    title: {
      type: String,
      default: '系统状态',
    },
  },
  data() {
    return {
      items: [
        { icon: 'dns', name: '工作节点数量', key: '$.NumWorkers' },
        { icon: 'dns', name: '服务器节点数量', key: '$.NumServers' },
        { icon: 'tag', name: '用户标签数量', key: '$.NumUserLabels' },
        { icon: 'tag', name: '条目标签的数量', key: '$.NumItemLabels' },
        { icon: 'trending_up', name: '热门条目更新时间', key: '$.PopularItemsUpdateTime' },
        { icon: 'trending_up', name: '最新条目更新时间', key: '$.LatestItemsUpdateTime' },
        { icon: 'group_work', name: '匹配模型训练时间', key: '$.MatchingModelFitTime' },
        { icon: 'group_work', name: '匹配模型精确率@10', key: '$.MatchingModelScore.Precision' },
        { icon: 'group_work', name: '匹配模型召回率@10', key: '$.MatchingModelScore.Recall' },
        { icon: 'group_work', name: '匹配模型NDCG数值@10', key: '$.MatchingModelScore.NDCG' },
        { icon: 'ads_click', name: '排序模型训练时间', key: '$.RankingModelFitTime' },
        { icon: 'ads_click', name: '排序模型精确率', key: '$.RankingModelScore.Precision' },
        { icon: 'ads_click', name: '排序模型召回率', key: '$.RankingModelScore.Recall' },
        { icon: 'ads_click', name: '排序模型AUC数值', key: '$.RankingModelScore.AUC' },
        { icon: 'travel_explore', name: '相邻用户索引召回', key: '$.UserNeighborIndexRecall' },
        { icon: 'travel_explore', name: '相邻条目索引召回', key: '$.ItemNeighborIndexRecall' },
        { icon: 'travel_explore', name: '匹配索引召回', key: '$.MatchingIndexRecall' },
      ],
      status: {},
    };
  },
  mounted() {
    axios.get('/api/dashboard/stats').then((res) => {
      this.status = res.data;
    });
  },
  methods: {
    query(path) {
      if (!(typeof path === 'string') || !path.startsWith('$')) {
        return '';
      }
      return jsonPath.query(this.status, path)[0];
    },
    format(data) {
      if (typeof data === 'number') {
        return data;
      }
      if (data === '') {
        return '';
      }
      return moment(String(data)).format('YYYY/MM/DD HH:mm');
    },
  },
};
</script>

