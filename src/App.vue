<template>
  <v-app>
    <v-main>
      <div>
        <v-toolbar
          color="black"
          dense
        >
          <v-toolbar-title class="ml-8">The Dream Realm</v-toolbar-title>
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
