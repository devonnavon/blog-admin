<template>
  <v-app>
    <v-main>
      <div v-if="loggedIn">
        <v-app-bar app>
          <span class="hidden-sm-and-up"></span>
          <v-toolbar-title>{{appTitle}}</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items class="hidden-xs-only">
            <v-btn
              text
              v-for="item in authMenuItems"
              :key="item.title"
              @click="menuClick(item.slug)"
            >
              <v-icon left dark>{{ item.icon }}</v-icon>
              {{ item.title }}
            </v-btn>
          </v-toolbar-items>
        </v-app-bar>
      </div>
      <div v-else>
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
      </div>
      <div v-if="selectedPage==='login'">
        <Login @loggedIn="login" />
      </div>
      <!-- <div v-if="selectedPage==='login'"><Login @loggedIn="login" /></div> -->
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
    ],
    authMenuItems: [
      { title: "Home", path: "", icon: "home", slug: "home" },
      { title: "Logout", path: "", icon: "face", slug: "logout" }
    ]
  }),

  methods: {
    login() {
      this.loggedIn = true;
      console.log("hey");
      console.log(this.loggedIn);
    },
    menuClick(slug) {
      this.selectedPage = slug;
      console.log(this.selectedPage);
    }
  }
};
</script>
