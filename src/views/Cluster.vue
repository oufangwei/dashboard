<template>
  <div class="main-content-container container-fluid px-4">
    <!-- Page Header -->
    <div class="page-header row no-gutters py-4">
      <div class="col-12 col-sm-4 text-center text-sm-left mb-0">
        <span class="text-uppercase page-subtitle">概览</span>
        <h3 class="page-title">集群</h3>
      </div>
    </div>

    <!-- Default Light Table -->
    <div class="row">
      <div class="col">
        <div class="card card-small mb-4">
          <div class="card-header border-bottom">
            <h6 class="m-0">活跃节点</h6>
          </div>
          <div class="card-body p-0 pb-3 text-center">
            <table class="table mb-0">
              <thead class="bg-light">
                <tr>
                  <th scope="col" class="border-0">类型</th>
                  <th scope="col" class="border-0">名称</th>
                  <th scope="col" class="border-0">IP</th>
                  <th scope="col" class="border-0">链接</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(node, idx) in nodes" :key="idx" >
                  <td>{{ node.Type }}</td>
                  <td>{{ node.Name }}</td>
                  <td>{{ node.IP }}</td>
                  <td>
                    <d-button-group size="small">
                      <d-button @click="openAPIDocs(node.IP, node.HttpPort)" v-if="node.Type == 'Server'" outline>API</d-button>
                      <d-button @click="openMetrics(node.IP, node.HttpPort)" outline>Metrics</d-button>
                    </d-button-group>
                  </td>
                </tr>
              </tbody>
            </table>
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
      nodes: null,
    };
  },
  mounted() {
    this.fetchNodes();
    this.timer = setInterval(this.fetchNodes, 1000);
  },
  beforeUnmount() {
    this.cancelAutoUpdate();
  },
  methods: {
    fetchNodes() {
      axios.get('/api/dashboard/cluster')
        .then((response) => {
          this.nodes = response.data;
        });
    },
    cancelAutoUpdate() {
      clearInterval(this.timer);
    },
    openMetrics(host, port) {
      window.open(`http://${host}:${port}/metrics`, '_blank');
    },
    openAPIDocs(host, port) {
      window.open(`http://${host}:${port}/apidocs`, '_blank');
    },
  },
};
</script>
