<template>
  <div>
    <div
      style="
      height: 10vh;
      padding: 10px;
      display: flex;
      flex-direction: row;
      justify-content: center;"
    >
      <!--Navigation Drawer trigger button only exists on mdAndDown-->
      <v-navigation-drawer
        v-model="drawer"
        absolute
        temporary
      >
        <v-list
          nav
          dense
          v-for="item in items"
          :key="item"
        >
          <v-list-item @click="routeTo(links[item])">
            <v-list-item-title>{{ item }}</v-list-item-title>
          </v-list-item>
        </v-list>
        <div style="display: flex; justify-content: center">
          <v-btn @click="dialog = true" v-if="!this.$store.state.auth.isLoggedIn">SIGN IN/UP</v-btn>
          <v-btn @click="dialog = true" v-if="this.$store.state.auth.isLoggedIn">MANAGE</v-btn>
        </div>
      </v-navigation-drawer>
      <!--This div element contains the banner text and icons-->
      <div style="width: 95vw; display: flex; flex-direction: column; justify-content: space-around; align-items: center">
        <div :class="$vuetify.breakpoint.smAndDown ? 'banner-smAndDown' : 'banner-normal'">
          <v-btn icon @click.stop="drawer = !drawer" v-show="$vuetify.breakpoint.smAndDown">
            <v-icon>mdi-menu</v-icon>
          </v-btn>
          <v-spacer></v-spacer>
          <h1 :class="$vuetify.breakpoint.smAndDown ? 'small-text' : 'normal-text'">SIMPLE CMS</h1>
          <v-spacer></v-spacer>
          <!-- 添加一个按钮来打开登录对话框 -->
          <v-btn icon @click="dialog = true" v-show="$vuetify.breakpoint.mdAndUp">
            <v-icon icon>
              mdi-account
            </v-icon>
          </v-btn>
          <div v-show="$vuetify.breakpoint.smAndDown"></div>
        </div>
      </div>
      <!-- 登录对话框 -->
      <v-dialog v-model="dialog" max-width="500px">
        <v-card>
          <!-- 在这里添加你的登录组件 -->
          <AuthComponent />
        </v-card>
      </v-dialog>
    </div>
    <v-card color="basil">
      <!--Don't know why class of md-and-up does not work-->
      <!--Use `activaTab` to bind highlight tab after each refresh-->
      <v-tabs
        v-model="activeTab"
        v-show="$vuetify.breakpoint.mdAndUp"
        background-color="transparent"
        grow
      >
        <v-tab
          v-for="item in items"
          :key="item"
          @click="routeTo(links[item])"
        >
          {{ item }}
        </v-tab>
      </v-tabs>
    </v-card>
  </div>
</template>

<style>
.banner-normal {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center
}

.banner-smAndDown {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center
}

.small-text {
  font-size: 1.5rem;
  display: flex;
  align-items: center
}

.normal-text {
  font-size: 3rem;
}
</style>

<script>
import AuthComponent from '@/components/AuthComponent';

export default {
  name: 'BannerComponent',

  components: {
    AuthComponent
  },

  data () {
    return {
      activeTab: 0,
      drawer: false,
      dialog: false,
      items: [
        'Home',
        'About Us',
        'Services',
        'Donate and Sponsor',
        'Ways to Support',
        'Events',
        'Contact Us',
        'More'
      ],
      links: {
        'Home': '/',
        'About Us': '/about',
        'Services': '/services',
        'Ways to Support': '/support',
        'Events': '/events',
        'Donate and Sponsor': '/donate',
        'Contact Us': '/contact'
        // 'More': '/more'
      }
    }
  },

  watch: {
    '$route': {
      immediate: true,
      handler(to, from) {
        // 在这里根据当前路由来设置 activeTab 的值
        // 例如，如果路由匹配 links 的某个元素，你可以找到它的索引并设置 activeTab
        const index = this.items.findIndex(item => this.links[item] === to.path);
        if (index >= 0) {
          this.activeTab = index;
        }
      }
    }
  },

  methods: {
    // Avoid a stupid error: NavigationDuplicated: Avoided redundant navigation to current location
    routeTo(route) {
      this.$router.push(route).catch(error => {
        if (error.name !== 'NavigationDuplicated') {
          // 如果不是 NavigationDuplicated 错误，抛出错误
          throw error;
        }
      });
    }
  }
};
</script>
