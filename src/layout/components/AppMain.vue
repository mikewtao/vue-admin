<template>
  <section class="app-main">
    <div style="margin-left:10px;">
      <div v-if="'home'===mainTabsActiveName" style="margin-top: 20px;">
        <el-card>
          <keep-alive>
            <router-view />
          </keep-alive>
        </el-card>
      </div>
      <el-tabs
        v-else
        v-model="mainTabsActiveName"
        @tab-click="selectedTabHandle"
        @tab-remove="removeTabHandle"
        :closable="true" >
        <el-tab-pane
          v-for="item in dynamicMainTabs"
          :key="item.name"
          :label="item.title"
          :name="item.name">
          <el-card>
            <keep-alive>
              <router-view v-if="item.name === mainTabsActiveName" />
            </keep-alive>
          </el-card>
        </el-tab-pane>
      </el-tabs>
    </div>
    <!--
    <transition name="fade-transform" mode="out-in">
      <router-view :key="key" />
    </transition>-->
  </section>
</template>

<script>
export default {
  name: 'AppMain',
  computed: {
    key() {
      return this.$route.fullPath
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
    },
    dynamicMainTabs: {
      get() {
        let tabs = this.$store.state.app.mainTabs
        tabs = tabs.filter(item => item.name !== 'home')
        return tabs
      }
    }
  },
  methods: {
    selectedTabHandle(tab) {
      tab = this.mainTabs.filter(item => item.name === tab.name)
      if (tab.length >= 1) {
        this.$router.push({ name: tab[0].name, query: tab[0].query, params: tab[0].params })
      }
    },
    removeTabHandle(tabName) {
      console.log(tabName)
      this.mainTabs = this.mainTabs.filter(item => item.name !== tabName)
      if (this.mainTabs.length >= 1) {
        // 当前选中tab被删除
        if (tabName === this.mainTabsActiveName) {
          var tab = this.mainTabs[this.mainTabs.length - 1]
          this.$router.push({ name: tab.name, query: tab.query, params: tab.params }, () => {
            this.mainTabsActiveName = this.$route.name
          })
        }
      } else {
        this.menuActiveName = ''
        this.$router.push({ name: 'home' })
      }
      console.log(this.mainTabs)
    }
  }
}
</script>

<style scoped>
.app-main {
  /*50 = navbar  */
  min-height: calc(100vh - 50px);
  width: 100%;
  position: relative;
  overflow: hidden;
}
.fixed-header+.app-main {
  padding-top: 50px;
}
</style>

<style lang="scss">
// fix css style bug in open el-dialog
.el-popup-parent--hidden {
  .fixed-header {
    padding-right: 15px;
  }
}
</style>
