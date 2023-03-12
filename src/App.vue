<template>
  <v-app>
    <v-main>
      <div>
        <v-toolbar
          color="black"
          dense
        >
          <span class="text-h6 ml-4 mr-1">
            Hazy Hues
          </span>
          <a href="https://meteron.ai" target="_blank" rel="noopener">
            <span class="font-italic subtitle-1 text--grey">
                by Meteron AI
            </span>
            <v-img></v-img>
          </a>

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
