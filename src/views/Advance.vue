<template>
  <div class="main-content-container container-fluid px-4">
    <!-- Page Header -->
    <div class="page-header row no-gutters py-4">
      <div class="col-12 col-sm-4 text-center text-sm-left mb-0">
        <span class="text-uppercase page-subtitle">概览</span>
        <h3 class="page-title">高级功能</h3>
      </div>
    </div>

    <!-- Default Light Table -->
    <div class="row">
      <div class="col">
        <div class="card card-small mb-4">
          <div class="card-header border-bottom">
            <h6 class="m-0">导出数据</h6>
          </div>
          <div class="card-body p-0 pb-3">
            <d-list-group flush>
              <d-list-group-item class="p-3">
                <d-row>
                  <d-col sm="12" md="2">
                    <a :href="'/api/bulk/users'">
                      <d-button class="advance_button" outline
                        >&nbsp;&nbsp;&nbsp;导出用户&nbsp;&nbsp;&nbsp;</d-button
                      >
                    </a>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label
                      >将用户数据导出到csv文件。格式为“user_id,
                      labels”。</label
                    >
                  </d-col>
                </d-row>
              </d-list-group-item>
              <d-list-group-item class="p-3">
                <d-row>
                  <d-col sm="12" md="2">
                    <a :href="'/api/bulk/items'">
                      <d-button class="advance_button" outline
                        >&nbsp;&nbsp;&nbsp;导出条目&nbsp;&nbsp;&nbsp;</d-button
                      >
                    </a>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label
                      >将条目数据导出到csv文件。格式为“item_id, is_hidden, categories,
                      timestamp, labels, description”。</label
                    >
                  </d-col>
                </d-row>
              </d-list-group-item>
              <d-list-group-item class="p-3">
                <d-row>
                  <d-col sm="12" md="2">
                    <a :href="'/api/bulk/feedback'">
                      <d-button outline>&nbsp;&nbsp;&nbsp;导出反馈&nbsp;&nbsp;&nbsp;</d-button>
                    </a>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label
                      >将反馈数据导出为csv文件。格式为
                      “feedback_type, user_id, item_id, timestamp”。</label
                    >
                  </d-col>
                </d-row>
              </d-list-group-item>
            </d-list-group>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <div class="card card-small mb-4">
          <div class="card-header border-bottom">
            <h6 class="m-0">导入数据</h6>
          </div>
          <div class="card-body p-0 pb-3">
            <d-list-group flush>
              <d-list-group-item class="p-3">
                <d-row>
                  <d-col sm="12" md="2">
                    <router-link :to="{ name: 'import_users' }">
                      <d-button outline
                        >&nbsp;&nbsp;&nbsp;导入用户&nbsp;&nbsp;&nbsp;</d-button
                      >
                    </router-link>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label>将用户数据导入智能推荐系统。</label>
                  </d-col>
                </d-row>
              </d-list-group-item>
              <d-list-group-item class="p-3">
                <d-row>
                  <d-col sm="12" md="2">
                    <router-link :to="{ name: 'import_items' }">
                      <d-button outline
                        >&nbsp;&nbsp;&nbsp;导入条目&nbsp;&nbsp;&nbsp;</d-button
                      >
                    </router-link>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label>将条目数据导入智能推荐系统。</label>
                  </d-col>
                </d-row>
              </d-list-group-item>
              <d-list-group-item class="p-3">
                <d-row>
                  <d-col sm="12" md="2">
                    <router-link :to="{ name: 'import_feedback' }">
                      <d-button outline>&nbsp;&nbsp;&nbsp;导入反馈&nbsp;&nbsp;&nbsp;</d-button>
                    </router-link>
                  </d-col>
                  <d-col sm="12" md="10">
                    <label>将反馈数据导入智能推荐系统。</label>
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
