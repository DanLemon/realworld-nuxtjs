<template>
  <div class="settings-page">
    <div class="container page">
      <div class="row">
  
        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">Your Settings</h1>
  
          <form>
            <fieldset>
                <fieldset class="form-group">
                  <input class="form-control" v-model="user.image" type="text" placeholder="URL of profile picture">
                </fieldset>
                <fieldset class="form-group">
                  <input class="form-control form-control-lg" v-model="user.username" type="text" placeholder="Your Name">
                </fieldset>
                <fieldset class="form-group">
                  <textarea class="form-control form-control-lg" v-model="user.bio" rows="8" placeholder="Short bio about you"></textarea>
                </fieldset>
                <fieldset class="form-group">
                  <input class="form-control form-control-lg" v-model="user.email" type="text" placeholder="Email">
                </fieldset>
                <fieldset class="form-group">
                  <input class="form-control form-control-lg" v-model="user.password" type="password" placeholder="Password">
                </fieldset>
                <button class="btn btn-lg btn-primary pull-xs-right" @click.prevent="updateSetting">
                  Update Settings
                </button>
               

            </fieldset>
          </form>
          <hr>
          <button class="btn btn-outline-danger" @click="logout">
            Or click here to logout.
          </button>
        </div>
  
      </div>
    </div>
  </div>
</template>
<script>
  import { getCurrentUser, updateUser } from '@/api/user'
  const Cookie = process.client ? require('js-cookie') : undefined
  export default {
    middleware: 'authenticated',
    name: 'SettingIndex',
    async asyncData ({ params }) {
      const { data } = await getCurrentUser()
      const { user } = data
      return {
        user
      }
    },
    methods: {
      logout () {
         // 保存用户的登录状态
         this.$store.commit('setUser', '')

          // 为了防止刷新页面数据丢失，我们需要把数据持久化
          Cookie.remove('user')

          // 跳转到首页
          this.$router.push('/')
      },
      async updateSetting () {
        const { data } = await updateUser({
          user: this.user
        })
        const { user } = data
        this.user = user
      }
    }
  }
</script>
<style>

</style>