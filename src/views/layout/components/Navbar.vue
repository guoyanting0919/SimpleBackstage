<template>
  <el-menu class="navbar" mode="horizontal">
    <div class="logo">
      <img class="user-avatar" :src="logo" />
    </div>
    <hamburger class="hamburger-container" :toggleClick="toggleSideBar" :isActive="sidebar.opened"></hamburger>
    <div class="breads">
      <!-- breads -->
      <el-breadcrumb separator="/" class="breadItems">
        <el-breadcrumb-item class="breadLink" v-for="item in $route.matched" :key="item.path">
          <router-link :to="item.path" class="breadLink">{{ item.meta.title || item.name }}</router-link>
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <el-dropdown class="avatar-container" @command="handleCommand" trigger="click">
      <div class="avatar-wrapper">
        歡迎您，{{name}}
        <i class="el-icon-caret-bottom"></i>
      </div>
      <el-dropdown-menu class="user-dropdown" slot="dropdown">
        <el-dropdown-item command="handleGoProfile">
          <span>個人中心</span>
        </el-dropdown-item>
        <el-dropdown-item disabled>
          <span>
            切換主題
            <el-switch
              disabled
              :active-value="1"
              :inactive-value="0"
              style="margin-left: 5px;"
              v-model="theme"
            />
          </span>
        </el-dropdown-item>
        <el-dropdown-item command="logout" divided>
          <span>退出</span>
        </el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
  </el-menu>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
import Hamburger from "@/components/Hamburger";
import logo from "@/assets/images/logo1.png";

export default {
  data: function () {
    return {
      logo: logo,
      theme: 1,
    };
  },
  components: {
    Hamburger,
  },
  computed: {
    ...mapGetters(["sidebar", "isIdentityAuth", "name", "themeStatus"]),
  },
  watch: {
    theme() {
      this.toggleClass(document.body, "custom-theme");
    },
  },
  mounted() {
    this.theme = Number(this.themeStatus);
    this.toggleClass(document.body, "custom-theme");
    // console.log(this.$route.matched);
  },
  methods: {
    ...mapActions(["signOutOidc", "saveTheme"]),
    toggleClass(element, className) {
      if (!element || !className) {
        return;
      }
      let classString = element.className;
      const nameIndex = classString.indexOf(className);
      if (nameIndex === -1) {
        classString += "" + className;
      } else {
        classString =
          classString.substr(0, nameIndex) +
          classString.substr(nameIndex + className.length);
      }
      element.className = this.theme === 1 ? classString : "";
      this.saveTheme(this.theme);
    },
    toggleSideBar() {
      this.$store.dispatch("ToggleSideBar");
    },
    logout() {
      if (this.isIdentityAuth) {
        this.signOutOidc();
      } else {
        this.$store.dispatch("LogOut").then(() => {
          location.reload(); // 為了重新實例化vue-router對象 避免bug
        });
      }
    },
    handleGoProfile() {
      this.$router.push("/profile");
    },
    handleCommand(name) {
      if (!name) return;
      this[name]();
    },
  },
};
</script>

<style rel="stylesheet/scss" lang="scss">
// .navbar {
//   display: flex;
//   align-items: center;
// }
// .breads {
//   height: 64px;
//   display: flex;
//   justify-content: center;
//   align-items: center;
//   margin-left: 0.5rem;
//   @include rwd($md) {
//     display: none;
//   }

//   .breadLink {
//     pointer-events: none;
//   }
// }
</style>
