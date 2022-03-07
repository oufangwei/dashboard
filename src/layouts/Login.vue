<template>
  <d-container>
    <div style="text-align: center;" class="align-center">
      <img style="width: 260px; margin-top: 60px; margin-bottom: 20px" src="@/assets/images/logo.png"/>
      <h5>欢迎来到智能推荐系统</h5>
      <d-card>
        <d-card-body>
          <d-form method="post" action="/login">
            <label class="sr-only" for="username">Username</label>
            <d-form-input id="username" name="user_name" class="mb-1" placeholder="用户名" />
            <label class="sr-only" for="password">Password</label>
            <d-form-input id="password" name="password" class="mt-2" type="password" placeholder="密码" />
            <d-alert v-if="this.$route.query.msg === 'incorrect'" theme="danger" class="mt-2"
                     :show="timeUntilDismissed"
                     @alert-dismissed="timeUntilDismissed = 0"
                     @alert-dismiss-countdown="handleTimeChange">用户名或密码不正确。</d-alert>
            <d-button class="mt-2" theme="primary" type="submit">登录</d-button>
          </d-form>
        </d-card-body>
      </d-card>
    </div>
  </d-container>
</template>

<style>
.align-center {
  margin: auto;
  max-width: 480px;
}
</style>

<script>
export default {
  data() {
    return {
      timeUntilDismissed: 5,
    };
  },
  methods: {
    handleTimeChange(time) {
      this.timeUntilDismissed = time;
    },
  },
};
</script>
