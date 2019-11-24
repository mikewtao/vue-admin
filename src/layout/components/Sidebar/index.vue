<template>
  <div :class="{'has-logo':showLogo}">
    <logo v-if="showLogo" :collapse="isCollapse" />
    <el-scrollbar wrap-class="scrollbar-wrapper">
      <el-menu
        :default-active="activeMenu"
        :collapse="isCollapse"
        :background-color="variables.menuBg"
        :text-color="variables.menuText"
        :unique-opened="false"
        :active-text-color="variables.menuActiveText"
        :collapse-transition="false"
        mode="vertical">
        <sidebar-item v-for="route in routes" :key="route.path" :item="route" :base-path="route.path" />
      </el-menu>
    </el-scrollbar>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import Logo from './Logo'
import SidebarItem from './SidebarItem'
import variables from '@/styles/variables.scss'

export default {
  components: { SidebarItem, Logo },
  computed: {
    ...mapGetters([
      'sidebar'
    ]),
    routes() {
      return this.$router.options.routes
    },
    activeMenu() {
      const route = this.$route
      const { meta, path } = route
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        return meta.activeMenu
      }
      return path
    },
    showLogo() {
      return this.$store.state.settings.sidebarLogo
    },
    variables() {
      return variables
    },
    isCollapse() {
      return !this.sidebar.opened
    },
    menuActiveName: {
      get() { return this.$store.state.app.menuActiveName },
      set(val) { this.$store.commit('app/updateMenuActiveName', val) }
    },
    mainTabs: {
      get() { return this.$store.state.app.mainTabs },
      set(val) { this.$store.commit('app/updateMainTabs', val) }
    },
    mainTabsActiveName: {
      get() { return this.$store.state.app.mainTabsActiveName },
      set(val) { this.$store.commit('app/updateMainTabsActiveName', val) }
    }
  },
  watch: {
    $route: 'routeHandle'
  },
  created() {
    this.routeHandle(this.$route)
  },
  methods: {
    routeHandle(route) {
      // tab选中, 不存在先添加
      console.log(route)
      let tab = this.mainTabs.filter(item => item.name === route.name)[0]
      if (!tab) {
        tab = {
          menuId: route.meta.menuId || route.name,
          name: route.name,
          title: route.meta.title,
          params: route.params,
          query: route.query
        }
        this.mainTabs = this.mainTabs.concat(tab)
      }
      this.menuActiveName = tab.menuId + ''
      this.mainTabsActiveName = tab.name
    }
  }
}
</script>
