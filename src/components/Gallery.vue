<template>
  <v-container class="fill-height">
    <v-responsive class="d-flex text-center fill-height">
      <div class="py-2" />

      <v-row justify="end">
        <v-dialog v-model="dialog" persistent width="768">
          <template v-slot:activator="{ props }">
            <v-btn color="primary" class="mr-4" v-bind="props"> Create </v-btn>
          </template>
          <v-card>
            <v-card-title class="mt-3">
              <span class="text-h5">Create New</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="12" md="12">
                    <p>What do you want to see?</p>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" sm="12" md="12">
                    <v-text-field
                      label="Prompt"
                      v-model="prompt"
                      hint="Tiny underwater complete world in a large glass bowl, water, sharp, photorealistic, detailed and intricate environment"
                      required
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
              <small>Image generation can take up to a minute 😅</small>
            </v-card-text>
            <v-card-actions class="mb-2 mr-2">
              <v-spacer></v-spacer>
              <v-btn color="secondary" variant="text" @click="dialog = false">
                Close
              </v-btn>
              <v-btn
                color="primary"
                variant="flat"
                @click="generate()"
                :loading="inProgress"
                :disabled="inProgress"
              >
                Generate
                <template v-slot:loader>
                  <span class="custom-loader">
                    <v-icon light>mdi-cached</v-icon>
                  </span>
                </template>
              </v-btn>
            </v-card-actions>

            <v-img
              v-if="inProgress && !newImage"
              class="ma-3"
              :src="`https://picsum.photos/10/6`"
              ><v-row class="fill-height ma-0" align="center" justify="center">
                <v-progress-circular
                  indeterminate
                  color="grey-lighten-5"
                ></v-progress-circular>
              </v-row>
            </v-img>

            <v-img
              v-if="newImage"
              :src="`${newImage.data}`"
              :lazy-src="`https://picsum.photos/10/6?image=${n * 5 + 10}`"
              aspect-ratio="1"
              cover
              class="bg-grey-lighten-2 ma-3"
            >
              <template v-slot:placeholder>
                <v-row class="fill-height ma-0" align="center" justify="center">
                  <v-progress-circular
                    indeterminate
                    color="grey-lighten-5"
                  ></v-progress-circular>
                </v-row>
              </template>
            </v-img>
          </v-card>
        </v-dialog>
      </v-row>

      <v-row>
        <v-col
          v-for="image in downloadedImages"
          :key="image.id"
          class="d-flex child-flex"
          cols="4"
        >
          <v-card width="100%">
            <v-hover>
              <template v-slot:default="{ isHovering, props }">
                <v-img
                  :src="`${image.data}`"
                  :lazy-src="`https://picsum.photos/10/6`"
                  aspect-ratio="1"
                  cover
                  v-bind="props"
                  class="align-end justify-start"
                >
                  <span class="text-subtitle-2 white" v-show="isHovering">{{ image.prompt }}</span>

                  <template v-slot:placeholder>
                    <v-row
                      class="fill-height ma-0"
                      align="center"
                      justify="center"
                    >
                      <v-progress-circular
                        indeterminate
                        color="grey-lighten-5"
                      ></v-progress-circular>
                    </v-row>
                  </template>
                </v-img>
              </template>
            </v-hover>
          </v-card>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<style>
.custom-loader {
  animation: loader 1s infinite;
  display: flex;
}
@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-o-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>

<script lang="ts">
export default {
  data: () => ({
    dialog: false,
  }),
}
</script>

<script setup lang="ts">
import { ref, watchEffect } from 'vue'

const API_URL = 'https://meteron.ai'
const API_ANON_TOKEN = 'eyJhbGciOiJSUzI1NiIsInR5cCI6IkpX'
const API_GENERATIONS_URL = `${API_URL}/v1/images/generations?status=completed`
const API_NEW_GENERATIONS_URL = `${API_URL}/v1/images/generations`

const prompt = ref('')
const inProgress = ref(false)
const newImageGen = ref({})
const newImage = ref(null)

// Generated images
const images = ref([])

// Download images to the browser
let downloadedImages = ref([])

watchEffect(async () => {
  var headers = new Headers({
    'Authorization': `Bearer ${API_ANON_TOKEN}`
  })

  images.value = await (await fetch(API_GENERATIONS_URL, { headers: headers })).json()

  // Iterate over the generated images list and call imageSource
  // to get the image data
  downloadedImages.value = await Promise.all(images.value.map(imageSource))
})

function generate() {
  watchEffect(async () => {
    inProgress.value = true

    const url = API_NEW_GENERATIONS_URL + '?user=user-x&cluster=lightning-cluster'
    var headers = new Headers({
      'Authorization': `Bearer ${API_ANON_TOKEN}`,
      // 'X-User': 'user-x',
      // 'X-Cluster': 'lightning-cluster',
      'Content-Type': 'application/json'
    })

    const data = {
      'prompt': prompt.value,
      'high_quality': 'true'
    }

    newImageGen.value = await (await fetch(url, { headers: headers, method: 'POST', body: JSON.stringify(data) })).json()

    // Download the data
    newImage.value = await imageSource(newImageGen.value)

    // Shutdown the progress bar
    inProgress.value = false
  })
}

async function imageSource(imageGen: any) {
  const resp = await (await fetch(imageGen.outputImages[0].url)).json()

  // Decode base64 prompt
  const request = JSON.parse(atob(imageGen.requestBody))

  return {
    id: imageGen.id,
    data: resp.image,
    prompt: request.prompt // Base64 decoded prompt
  }
}

</script>
