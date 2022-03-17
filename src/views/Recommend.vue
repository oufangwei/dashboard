<template>
  <d-container fluid class="main-content-container px-4">
    <!-- Page Header -->
    <d-row no-gutters class="page-header py-4">
      <d-col col sm="4" class="text-center text-sm-left mb-4 mb-sm-0">
        <span class="text-uppercase page-subtitle">用户ID:</span>
        <h3 class="page-title">{{ user_id }}</h3>
      </d-col>
    </d-row>

    <div class="row">
      <div class="col">
        <div class="card card-small mb-4">
          <div class="card-header border-bottom">
            <h6 class="m-0">用户信息</h6>
          </div>
          <div class="card-body p-0 pb-3">
            <d-list-group flush>
              <d-list-group-item class="p-3" v-if="current_user != null">
                <d-row>
                  <d-col sm="12" md="2">
                    <label>用户ID</label>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label class="text-light">{{ user_id }}</label>
                  </d-col>
                </d-row>
                <d-row>
                  <d-col sm="12" md="2">
                    <label>用户姓名</label>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label class="text-light">{{ current_user.User_name }}</label>
                  </d-col>
                </d-row>
                <d-row>
                  <d-col sm="12" md="2">
                    <label>用户性别</label>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label class="text-light">{{ current_user.Gender }}</label>
                  </d-col>
                </d-row>
                <d-row>
                  <d-col sm="12" md="2">
                    <label>用户标签（用户画像）</label>
                  </d-col>
                  <d-col sm="12" md="10">
                    <d-badge
                      outline
                      theme="primary"
                      v-for="(label, idx) in current_user.Labels"
                      :key="idx"
                    >
                      {{ label }}
                    </d-badge>
                  </d-col>
                </d-row>
              </d-list-group-item>
            </d-list-group>
          </div>
        </div>
      </div>
    </div>

    <d-row>
      <d-col lg="6" md="12" sm="12" class="mb-4">
        <bo-top-items :title="'用户正反馈的问题/活动'" :items="feedback"/>
      </d-col>

      <d-col lg="6" md="12" sm="12" class="mb-4">

        <bo-user-recommend :user_id = "user_id"/>


      </d-col>
    </d-row>
  </d-container>
</template>

<script>
import TopItems from '@/components/common/TopItemsCard.vue';
import UserRecommend from '@/components/common/UserRecommend.vue';

const axios = require('axios');

export default {
  components: {
    boTopItems: TopItems,
    boUserRecommend: UserRecommend,
  },
  data() {
    return {
      cacheSize: 100,
      user_id: null,
      current_user: null,
      feedback: [],
      recommends: [],
      feedbackTypes: [],
    };
  },
  created() {
    this.user_id = this.$route.params.user_id;
  },
  mounted() {
    axios.get(`/api/user/${this.user_id}`).then((response) => {
      this.current_user = response.data;
    });
    // load config
    axios.get('/api/dashboard/config')
      .then((response) => {
        this.cacheSize = response.data.Database.CacheSize;
        this.feedbackTypes = response.data.Database.PositiveFeedbackType;
        const requests = [];
        this.feedbackTypes.forEach((feedbackType) => {
          requests.push(axios.get(`/api/dashboard/user/${this.user_id}/feedback/${feedbackType}/`));
        });
        axios.all(requests).then(axios.spread((...responses) => {
          const feedbackItems = [];
          responses.forEach((response) => {
            response.data.forEach((feedback) => {
              feedback.Item.Timestamp = feedback.Timestamp;
              feedbackItems.push(feedback.Item);
            });
          })
          this.feedback = feedbackItems.sort((a, b) => (a.Timestamp < b.Timestamp) ? 1 : -1);
        }))
      });
  },
};
</script>
