<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Poll Overview</router-link>|
      <router-link to="/newpoll">Create Poll</router-link>|
      <router-link to="/about">About</router-link>| <router-link to="/login">Login</router-link>|
      <a @click.prevent="logOut">LogOut</a>
    </div>
    <router-view />
    <confirmationModal />
  </div>
</template>

<script>
import { auth } from './utility/firebase';
import confirmationModal from './components/basic/ConfirmationModal.vue';

export default {
  components: {
    confirmationModal,
  },
  mounted() {
    // this method creates an observer that should be triggered on signIn and signOut
    auth.onAuthStateChanged(user => {
      console.log('observer triggered');
      if (!user) {
        this.$store.commit('setUser', null);
        if (this.$route.meta.admin || this.$route.meta.auth) this.$router.push({ path: '/login' });
      } else {
        user.getIdTokenResult().then(tokenResult => {
          user.admin = tokenResult.claims.admin;
          this.$store.commit('setUser', user);
          if (this.$route.meta.guest) {
            this.$router.push({ path: '/' });
          }
        });
      }
    });
  },
  methods: {
    async logOut() {
      await auth.signOut();
      try {
        console.log('logged user out');
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 10px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
label {
  text-align: left;
}
</style>
