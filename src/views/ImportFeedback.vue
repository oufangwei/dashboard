<template>
  <div class="main-content-container container-fluid px-4">
    <!-- Page Header -->
    <div class="page-header row no-gutters py-4">
      <div class="col-12 col-sm-4 text-center text-sm-left mb-0">
        <span class="text-uppercase page-subtitle">预览</span>
        <h3 class="page-title">导入反馈</h3>
      </div>
    </div>
    <d-alert
      :theme="alertTheme"
      :show="timeUntilDismissed"
      dismissible
      @alert-dismissed="timeUntilDismissed = 0"
      @alert-dismiss-countdown="handleTimeChange"
      >{{ alertText }}</d-alert
    >
    <div class="row">
      <div class="col">
        <div class="card card-small mb-4">
          <div class="card-header">
            <d-form validated>
              <d-form-row>
                <d-col md="9" class="form-group">
                  <strong class="text-muted d-block mb-2">文件</strong>
                  <div class="custom-file mb-3">
                    <input
                      type="file"
                      class="custom-file-input"
                      id="csvFile"
                      @change="loadFile"
                      required
                    />
                    <label class="custom-file-label" for="customFile2">{{
                      fileName
                    }}</label>
                    <d-form-invalid-feedback
                      >上传本地csv文件。</d-form-invalid-feedback
                    >
                  </div>
                </d-col>
                <d-col md="3" class="form-group">
                  <label for="fieldSeparator">字段分隔符</label>
                  <d-input
                    id="fieldSeparator"
                    type="text"
                    v-model="fieldSeparator"
                    required
                  />
                  <d-form-invalid-feedback
                    >需要填写字段分隔符！</d-form-invalid-feedback
                  >
                </d-col>
              </d-form-row>
            </d-form>
            <d-form @submit.prevent="uploadFile">
              <d-form-row>
                <d-col md="3">
                  <strong class="text-muted d-block mb-2"
                    >字段匹配</strong
                  >
                  <d-input-group prepend="反馈类型" class="mb-3">
                    <d-select v-model="feedbackTypeColIdx">
                      <option
                        v-for="(name, idx) in previewHeader"
                        :key="idx"
                        :value="idx"
                      >
                        {{ name }}
                      </option>
                    </d-select>
                  </d-input-group>
                </d-col>
                <d-col md="3">
                  <strong class="text-muted d-block mb-2">&nbsp;</strong>
                  <d-input-group prepend="用户ID" class="mb-3">
                    <d-select v-model="userIdColIdx">
                      <option
                        v-for="(name, idx) in previewHeader"
                        :key="idx"
                        :value="idx"
                      >
                        {{ name }}
                      </option>
                    </d-select>
                  </d-input-group>
                </d-col>
                <d-col md="3">
                  <strong class="text-muted d-block mb-2">&nbsp;</strong>
                  <d-input-group prepend="问题/活动ID" class="mb-3">
                    <d-select v-model="itemIdColIdx">
                      <option
                        v-for="(name, idx) in previewHeader"
                        :key="idx"
                        :value="idx"
                      >
                        {{ name }}
                      </option>
                    </d-select>
                  </d-input-group>
                </d-col>
                <d-col md="3">
                  <strong class="text-muted d-block mb-2">&nbsp;</strong>
                  <d-input-group prepend="时间戳" class="mb-3">
                    <d-select v-model="timestampColIdx">
                      <option
                        v-for="(name, idx) in previewHeader"
                        :key="idx"
                        :value="idx"
                      >
                        {{ name }}
                      </option>
                    </d-select>
                  </d-input-group>
                </d-col>
                <d-col md="10" class="form-group">
                  <d-checkbox v-model="hasHeader"
                    >第一行是标题。</d-checkbox
                  >
                </d-col>
                <d-col md="2"> <d-button>确认导入</d-button></d-col>
              </d-form-row>
            </d-form>
          </div>
          <div class="progress" v-if="progressShow">
            <div
              class="progress-bar progress-bar-striped progress-bar-animated"
              role="progressbar"
              aria-valuenow="75"
              aria-valuemin="0"
              aria-valuemax="100"
              style="width: 100%"
            ></div>
          </div>
          <div class="card-body p-0 pb-3">
            <table class="table mb-0">
              <thead class="bg-light">
                <tr>
                  <th
                    scope="col"
                    class="border-0"
                    v-for="(name, idx) in previewHeader"
                    :key="idx"
                  >
                    {{ mappedColumNames[idx]
                    }}<span class="text-semibold">({{ name }})</span>
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(line, row_idx) in previewBody" :key="row_idx">
                  <td v-for="(value, col_idx) in line" :key="col_idx">
                    <div v-if="mappedColumNames[col_idx] == 'Labels'">
                      <d-badge
                        outline
                        theme="primary"
                        v-for="(label, idx) in splitLabels(
                          value,
                          labelSeparator
                        )"
                        :key="idx"
                      >
                        {{ label }}
                      </d-badge>
                    </div>
                    <div v-else>
                      {{ value }}
                    </div>
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
import moment from 'moment';
import utils from '@/utils/index';

const axios = require('axios');

export default {
  data() {
    return {
      fieldSeparator: ',',
      fileName: '选择文件...',
      hasHeader: true,
      text: '',
      feedbackTypeColIdx: 0,
      userIdColIdx: 1,
      itemIdColIdx: 2,
      timestampColIdx: 3,
      progressShow: false,
      alertTheme: 'danger',
      alertText: null,
      duration: 5,
      timeUntilDismissed: 0,
    };
  },
  computed: {
    mappedColumNames() {
      const header = this.previewHeader;
      const names = [];
      for (let i = 0; i < header.length; i += 1) {
        if (i === this.feedbackTypeColIdx) {
          names.push('FeedbackType');
        } else if (i === this.userIdColIdx) {
          names.push('UserId');
        } else if (i === this.itemIdColIdx) {
          names.push('ItemId');
        } else if (i === this.timestampColIdx) {
          names.push('Timestamp');
        } else {
          names.push('');
        }
      }
      return names;
    },
    previewHeader() {
      return this.previewTable[0];
    },
    previewBody() {
      return this.previewTable.slice(1);
    },
    previewTable() {
      if (this.fieldSeparator.length === 0) {
        return [];
      }
      let numCols = 0;
      // split fields
      const table = utils
        .parseLines(this.text, this.fieldSeparator)
        .slice(0, -1);
      table.forEach((fields) => {
        numCols = Math.max(numCols, fields.length);
      });
      // add header
      if (!this.hasHeader) {
        const header = [];
        for (let i = 0; i < numCols; i += 1) {
          header.push(i.toString());
        }
        table.unshift(header);
      }
      return table;
    },
  },
  methods: {
    loadFile(event) {
      const file = event.target.files[0];
      // load file name
      this.fileName = file.name;
      // load file content
      const reader = new FileReader();
      reader.readAsText(file.slice(0, 1024));
      reader.onload = (e) => {
        this.text = e.target.result;
      };
    },
    format_date_time(timestamp) {
      if (timestamp == "") {
        return "";
      }
      return moment(String(timestamp)).format('YYYY/MM/DD HH:mm');
    },
    handleTimeChange(time) {
      this.timeUntilDismissed = time;
    },
    showDanger(mesage) {
      this.timeUntilDismissed = this.duration;
      this.alertTheme = 'danger';
      this.alertText = mesage;
    },
    showSuccess(mesage) {
      this.timeUntilDismissed = this.duration;
      this.alertTheme = 'success';
      this.alertText = mesage;
    },
    resetProgressBar() {
      this.progressTotal = 100;
      this.progressLoaded = 0;
    },
    resetTable() {
      this.table = [];
    },
    uploadFile() {
      const formData = new FormData();
      // 1. field separator must be a single character
      if (this.fieldSeparator.length !== 1) {
        this.showDanger('field separator must be a single character');
        return;
      }
      formData.append('sep', this.fieldSeparator);
      formData.append('has-header', this.hasHeader);
      formData.append(
        'format',
        utils.mapFields('fuit', [
          this.feedbackTypeColIdx,
          this.userIdColIdx,
          this.itemIdColIdx,
          this.timestampColIdx,
        ]),
      );
      // 2. file must be chosen
      const csvfile = document.querySelector('#csvFile');
      if (csvfile.files.length === 0) {
        this.showDanger('必须选择文件');
        return;
      }
      formData.append('file', csvfile.files[0]);
      // start upload
      this.progressShow = true;
      axios
        .post('/api/bulk/feedback', formData)
        .then((response) => {
          // import items successfully
          this.showSuccess(response.data);
          this.resetProgressBar();
          this.resetTable();
          this.progressShow = false;
        })
        .catch((error) => {
          // receive error
          this.showDanger(error.response.data);
          this.resetProgressBar();
          this.progressShow = false;
        });
    },
  },
};
</script>
