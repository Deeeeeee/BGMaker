<template>
  <v-app>
    <!--<v-navigation-drawer-->
      <!--persistent-->
      <!--:mini-variant="miniVariant"-->
      <!--:clipped="clipped"-->
      <!--v-model="drawer"-->
      <!--enable-resize-watcher-->
      <!--fixed-->
      <!--app-->
    <!--&gt;-->
      <!--<v-list>-->
        <!--<v-list-tile value="true" v-for="(item, i) in items" :key="i">-->
          <!--<v-list-tile-action>-->
            <!--<v-icon v-html="item.icon"></v-icon>-->
          <!--</v-list-tile-action>-->
          <!--<v-list-tile-content>-->
            <!--<v-list-tile-title v-text="item.title"></v-list-tile-title>-->
          <!--</v-list-tile-content>-->
        <!--</v-list-tile>-->
      <!--</v-list>-->
    <!--</v-navigation-drawer>-->
    <v-toolbar app :clipped-left="clipped">
      <!--<v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>-->
      <v-toolbar-title v-text="title"></v-toolbar-title>
    </v-toolbar>
    <v-content>
      <router-view/>
    </v-content>

  </v-app>
</template>

<script>
  export default {
    data () {
      return {
        clipped: false,
        drawer: false,
        fixed: false,
        items: [{
          icon: 'bubble_chart',
          title: 'Inspire'
        }],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'BGMaker'
      }
    },
    name: 'App'
  }
</script>
