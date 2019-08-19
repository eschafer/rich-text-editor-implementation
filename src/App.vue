<template>
  <div id="app">
    <h3>Welcome to Netlify Identity Demo in Vue.js</h3>
    <div v-if="isLoggedIn">
      <p>Hello {{ username }}</p>
      <p>
        <button @click="triggerNetlifyIdentityAction('logout')">Log Out</button>
      </p>
    </div>
    <div v-else>
      <p>You are not logged in.</p>
      <p>
        <button @click="triggerNetlifyIdentityAction('login')">Log In</button>
        <button @click="triggerNetlifyIdentityAction('signup')">Sign Up</button>
      </p>
    </div>
    <ul>
      <li>
        <router-link :to="{name:'Home'}">Home Page</router-link>
      </li>
      <li>
        <router-link :to="{name:'Public'}">Public Page</router-link>
      </li>
      <li>
        <router-link :to="{name:'Protected'}">Protected Page</router-link>
      </li>
    </ul>
    <router-view></router-view>
  </div>
</template>

<script>
import Home from "@/components/Home";
import Public from "@/components/Public";
import Protected from "@/components/Protected";
import netlifyIdentity from "netlify-identity-widget";
import { mapGetters, mapActions } from "vuex";
netlifyIdentity.init({
  APIUrl: "https://eschafer-rich-text-editor-implementation.netlify.com/.netlify/identity",
  logo: true // you can try false and see what happens
});
export default {
  name: "app",
  components: {
    Home,
    Public,
    Protected
  },
  computed: {
    ...mapGetters("user", {
      isLoggedIn: "getUserStatus",
      user: "getUser"
    }),
    username() {
      return this.user ? this.user.username : ", there!";
    }
  },
  data: () => ({
    currentUser: null
  }),
  methods: {
    ...mapActions("user", {
      updateUser: "updateUser"
    }),
    triggerNetlifyIdentityAction(action) {
      if (action == "login" || action == "signup") {
        netlifyIdentity.open(action);
        netlifyIdentity.on(action, user => {
          this.currentUser = {
            username: user.user_metadata.full_name,
            email: user.email,
            access_token: user.token.access_token,
            expires_at: user.token.expires_at,
            refresh_token: user.token.refresh_token,
            token_type: user.token.token_type
          };
          this.updateUser({
            currentUser: this.currentUser
          });
          netlifyIdentity.close();
        });
      } else if (action == "logout") {
        this.currentUser = null;
        this.updateUser({
          currentUser: this.currentUser
        });
        netlifyIdentity.logout();
        this.$router.push({ name: "Home" });
      }
    }
  },
  created: function() {
      netlifyIdentity.on('login', user => {
      this.currentUser = {
        username: user.user_metadata.full_name,
        email: user.email,
        access_token: user.token.access_token,
        expires_at: user.token.expires_at,
        refresh_token: user.token.refresh_token,
        token_type: user.token.token_type
      };
      this.updateUser({
        currentUser: this.currentUser
      });
      netlifyIdentity.close();
    });
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

<!--
<template>
  <div id="app">
    <HelloWorld msg="Hello Vue in CodeSandbox!"/>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",
  components: {
    HelloWorld
  }
};
</script>

<style>
#app {
  font-family: system-ui, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  border: 1px solid black;
  padding: 0 1em;
}
</style>
-->
