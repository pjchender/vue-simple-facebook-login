<template>
  <div id="app">
    <img src="./assets/logo.png">
    <hello></hello>
    <div class="container">
        <div class="row justify-content-center">
          <div class="col-2" v-if="!authorized">
            <button type="button" class="btn btn-outline-success" @click="login">Login</button>
          </div>
          <div class="col-2" v-else>
            <button type="button" class="btn btn-outline-success" @click="logout">Logout</button>
          </div>
        </div>
    </div>
  </div>
</template>

<script>
import Hello from './components/Hello'

export default {
  name: 'app',
  components: {
    Hello
  },
  data () {
    return {
      profile: {},
      authorized: false
    }
  },
  methods: {
    getProfile () {
      let vm = this
      FB.api('/me?fields=name,id,email', function (response) {
        vm.$set(vm, 'profile', response)
      })
    },
    login () {
      let vm = this
      FB.login(function (response) {
        vm.statusChangeCallback(response) 
      }, {
        scope: 'email, public_profile',
        return_scopes: true
      })
    },
    logout () {
      let vm = this
      FB.logout(function (response) {
        vm.statusChangeCallback(response)
      })
    },
    statusChangeCallback (response) {
      let vm = this
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
    
    // facebook 初始化
    window.fbAsyncInit = function() {
      FB.init({
        appId: '135862346985755',
        cookie: true,
        xfbml: true,
        version: 'v2.9'
      });
      FB.AppEvents.logPageView();

      // Get FB Login Status
      FB.getLoginStatus(response => {
        vm.statusChangeCallback(response)
      })
    };
  }
}
</script>

<style lang="scss" scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
button {
  cursor: pointer;
}
</style>
