<template>
  <div id="app">
    <div v-if="ready" class="">
      <button v-if="authorized" @click="logout()" class="btn btn-default">Logout</button>
      <button v-else="authorized" @click="login()" class="btn btn-default">Login</button>

      <br><br>

      <div v-if="authorized" class="">
        <img src="./assets/logo.png">
        <hello :profile="profile"></hello>
      </div>
    </div>

    <div v-else="ready" class="">
      <center>
        <img src="/static/shiba_walk.gif" alt="" width="400px"/>
      </center>
    </div>
  </div>
</template>

<script>
/* global FB */
import Hello from './components/Hello'

export default {
  name: 'app',
  components: {
    Hello
  },
  data () {
    return {
      profile: {},
      ready: false,
      authorized: false
    }
  },
  methods: {
    getProfile () {
      let vm = this
      FB.api('/me', function (response) {
        console.log(response)
        vm.$set(vm, 'profile', response)
      })
    },
    login () {
      let vm = this
      FB.login(function (response) {
        vm.statusChangeCallback(response)
      }, {scope: 'publish_actions'})
    },
    logout () {
      let vm = this
      FB.logout(function (response) {
        vm.statusChangeCallback(response)
      })
    },
    statusChangeCallback (response) {
      let vm = this
      vm.ready = true
      console.log('statusChangeCallback')
      console.log(response)
      if (response.status === 'connected') {
        vm.authorized = true
        vm.getProfile()
      } else if (response.status === 'not_authorized') {
        vm.authorized = false
      } else {
        vm.authorized = false
      }
    }
  },
  mounted () {
    let vm = this
    window.fbAsyncInit = () => {
      FB.init({
        appId: '1704488683203367',
        cookie: true,
        xfbml: true,
        version: 'v2.8'
      })
      FB.getLoginStatus(function (response) {
        vm.statusChangeCallback(response)
      })
    }
  }
}
</script>

<style>
html, body {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background-color: #EDF7FF;
  text-align: center;
  padding: 30px;
}
.box {
  background-color: #B6E3F6;
  margin-bottom: 20px;
  padding: 20px;
  border-radius: 5px;
}
</style>
