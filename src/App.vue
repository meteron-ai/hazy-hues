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
          <v-spacer></v-spacer>
          <div class="align-end">
            <v-btn
              class="ma-2 white--text"
              outlined
              target="_blank"
              href="https://github.com/meteron-ai/hazy-hues"
            >
              <v-icon class="mr-2">mdi-github</v-icon>
              GitHub
            </v-btn>
          </div>
        </v-toolbar>
      </div>
      <Gallery />

      <v-footer padless>
        <v-col
          class="text-start"
          cols="12"
        >
          Powered by <strong><a href="https://meteron.ai" target="_blank">Meteron</a></strong>, <strong><a href="https://lightning.ai" target="_blank">Lightning AI</a></strong>
          and <strong><a href="https://cloudflare.com" target="_blank">Cloudflare</a></strong>.
        </v-col>
      </v-footer>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import Gallery from '@/components/Gallery.vue'
</script>
