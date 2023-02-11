<template>
  <v-app>
    <v-main>
      <div>
        <v-toolbar
          color="black"
          dense
        >
          <v-app-bar-nav-icon></v-app-bar-nav-icon>
          <v-toolbar-title>The Dream Shop</v-toolbar-title>
          <v-spacer></v-spacer>
        </v-toolbar>
      </div>
      <Gallery />
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import Gallery from '@/components/Gallery.vue'
</script>
