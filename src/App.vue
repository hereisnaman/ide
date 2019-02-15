<template>
  <div id="app">
    <router-view></router-view>
    <notifications/>
  </div>
</template>

<script>
  import Editor from './components/Editor'
  import { setToken } from './utils/api'

  export default {
    name: 'app',
    components: {
      Editor
    },
    created() {
      if (this.$store.state.user.isAuthenticated) {
        setToken(this.$store.state.user.token)
      }
      if (this.$route.name != 'saved') {
        this.$store.commit('resetCode')
      }
    },
    mounted() {
      let mutationsToSubscribe = [
        'changeTheme',
        'changeFont',
        'changeFontSize',
        'resetEditor',
      ]

      let fileChangeMutations = [
        'changeLanguage',
        'updateCode',
        'setCode',
        'uploadCode',
        'changeCustomInput',
        'resetCode',
        'setCodeId',
      ]

      this.$store.subscribe((mutation, state) => {
        if (mutationsToSubscribe.includes(mutation.type) && !state.isChanged) {
          this.$store.commit('setIsChanged', true)
        }

        if (
          state.user.isAuthenticated &&
          state.user.currentUser &&
          state.user.currentUser.wakatime_api_key &&
          fileChangeMutations.includes(mutation.type)
        ) {
          this.$store.dispatch('pingWakatime');
        }
      })
    }
  }

</script>
<style src="bootstrap/dist/css/bootstrap.css"></style>
<style src="font-awesome/css/font-awesome.css"></style>
<style src="./assets/css/whole.css"></style>
<style scoped>
  #app {
    background: #202020;
  }
</style>
