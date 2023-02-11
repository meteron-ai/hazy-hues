<template>
  <v-container class="fill-height">
    <v-responsive class="d-flex text-center fill-height">


      <div class="py-14" />


      <v-row>
        <v-col
          v-for="image in downloadedImages"
          :key="image.id"
          class="d-flex child-flex"
          cols="4"
        >
          <v-img
            :src="`${image.data}`"
            :lazy-src="`https://picsum.photos/10/6?image=${n * 5 + 10}`"
            aspect-ratio="1"
            cover
            class="bg-grey-lighten-2"
          >
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
        </v-col>

        <v-col
          v-for="n in 9"
          :key="n"
          class="d-flex child-flex"
          cols="4"
        >
          <v-img
            :src="`https://picsum.photos/500/300?image=${n * 5 + 10}`"
            :lazy-src="`https://picsum.photos/10/6?image=${n * 5 + 10}`"
            aspect-ratio="1"
            cover
            class="bg-grey-lighten-2"
          >
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
        </v-col>
      </v-row>


    </v-responsive>
  </v-container>
</template>

<script setup lang="ts">
import { ref, watchEffect } from 'vue'

const API_URL = 'https://meteron.ai'

// Generated images
const images = ref(null)

// Download images to the browser
let downloadedImages = ref([])

watchEffect(async () => {
  const url = `${API_URL}/v1/images/generations?status=completed`

  var headers = new Headers({
      'Authorization': `Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpX`
  })

  images.value = await (await fetch(url, {headers: headers})).json()

  // Iterate over the generated images list and call imageSource
  // to get the image data
  downloadedImages.value = await Promise.all(images.value.map(imageSource))
})

async function imageSource(imageGen) {
  const resp = await (await fetch(imageGen.outputImages[0].url)).json()
  return {
    id: imageGen.id,
    data: resp.image
  }
}

</script>
