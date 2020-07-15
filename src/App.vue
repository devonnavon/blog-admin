<template>
  <v-app>
    <v-main>
      <v-app-bar app>
        <span class="hidden-sm-and-up"></span>
        <v-toolbar-title>{{appTitle}}</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items class="hidden-xs-only">
          <v-btn
            text
            v-for="item in menuItems"
            :key="item.title"
            :to="item.path"
            @click="menuClick(item.slug)"
          >
            <v-icon left dark>{{ item.icon }}</v-icon>
            {{ item.title }}
          </v-btn>
        </v-toolbar-items>
      </v-app-bar>
      <div v-if="selectedPage==='login'">
        <Login @loggedIn="login" />
      </div>
      <div v-if="selectedPage==='sign_up'">sign up!</div>
      <div v-if="selectedPage==='home'">home bro</div>
      <div v-if="selectedPage==='logout'">log out bro</div>
    </v-main>
  </v-app>
</template>

<script>
import Login from "./components/Login";

export default {
  name: "App",

  components: {
    Login
  },

  data: () => ({
    appTitle: "B L o G",
    blogs: [],
    loggedIn: false,
    sidebar: false,
    selectedPage: "login",
    menuItems: [
      { title: "Sign Up", path: "", icon: "face", slug: "sign_up" },
      { title: "Login", path: "", icon: "lock_open", slug: "login" }
    ]
  }),

  methods: {
    login() {
      this.loggedIn = true;
      this.selectedPage = "home";
      this.menuItems = [
        { title: "Home", path: "", icon: "home", slug: "home" },
        { title: "Logout", path: "", icon: "face", slug: "logout" }
      ];
      console.log(this.loggedIn);
    },
    logout() {
      this.menuItems = [
        { title: "Sign Up", path: "", icon: "face", slug: "sign_up" },
        { title: "Login", path: "", icon: "lock_open", slug: "login" }
      ];
      localStorage.clear();
    },
    menuClick(slug) {
      if (slug === "logout") {
        this.selectedPage = "login";
        this.logout();
      } else this.selectedPage = slug;
    }
  }
};
</script>
