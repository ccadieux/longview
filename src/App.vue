<template>
  <v-app :dark="isDark">
    <v-toolbar app color="primary" dark>
      <v-toolbar-title class="headline">
        <span>Manage Security</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        flat
        @click="redTheme"
      >
        <span class="mr-2">Red theme</span>
      </v-btn>
      <v-btn
        flat
        @click="lightTheme"
      >
        <span class="mr-2">Light theme</span>
      </v-btn>
      <v-btn
        flat
        @click="darkTheme"
      >
        <span class="mr-2">Dark theme</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <AuditLog/>
    </v-content>
  </v-app>
</template>

<script>
import AuditLog from './components/AuditLog';

export default {
  name: 'App',
  components: {
    AuditLog
  },
  data() {
    return {
      isDark: false
    };
  },
  methods: {
    redTheme: function() {
      this.$vuetify.theme.primary = '#c72946';
      this.$vuetify.theme.secondary = '#404455';
      this.isDark = false;
    },
    lightTheme: function() {
      this.$vuetify.theme.primary = '#404455';
      this.$vuetify.theme.secondary = '#404455';
      this.isDark = false;
    },
    darkTheme: function() {
      this.$vuetify.theme.primary = '#303341';
      this.$vuetify.theme.secondary = '#626678';
      this.isDark = true;
    }
  }
};
</script>
<style lang="scss">
.side-by-side {
  display: flex;
}
</style>
