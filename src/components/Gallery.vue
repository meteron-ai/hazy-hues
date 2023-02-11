<template>
  <v-container class="fill-height">
    <v-responsive class="d-flex text-center fill-height">
      <div class="py-2" />

      <v-row justify="end">
        <v-dialog v-model="dialog" persistent max-width="600px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" class="mr-4" dark v-bind="attrs" v-on="on">
              Create
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">User Profile</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      label="Legal first name*"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      label="Legal middle name"
                      hint="example of helper text only on focus"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field
                      label="Legal last name*"
                      hint="example of persistent helper text"
                      persistent-hint
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field label="Email*" required></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      label="Password*"
                      type="password"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-select
                      :items="['0-17', '18-29', '30-54', '54+']"
                      label="Age*"
                      required
                    ></v-select>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-autocomplete
                      :items="[
                        'Skiing',
                        'Ice hockey',
                        'Soccer',
                        'Basketball',
                        'Hockey',
                        'Reading',
                        'Writing',
                        'Coding',
                        'Basejump',
                      ]"
                      label="Interests"
                      multiple
                    ></v-autocomplete>
                  </v-col>
                </v-row>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialog = false">
                Close
              </v-btn>
              <v-btn color="blue darken-1" text @click="dialog = false">
                Save
              </v-btn>
            </v-card-actions>
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
            <v-img
              :src="`${image.data}`"
              :lazy-src="`https://picsum.photos/10/6?image=${n * 5 + 10}`"
              aspect-ratio="1"
              cover
              class="bg-grey-lighten-2"
            >
              <v-card-subtitle class="white--text align-end">{{
                image.prompt
              }}</v-card-subtitle>

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
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script setup lang="ts">
import { ref, watchEffect } from 'vue'

const API_URL = 'https://meteron.ai'

let dialog = false

// Generated images
const images = ref([])

// Download images to the browser
let downloadedImages = ref([])

watchEffect(async () => {
  const url = `${API_URL}/v1/images/generations?status=completed`

  var headers = new Headers({
    'Authorization': `Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpX`
  })

  images.value = await (await fetch(url, { headers: headers })).json()

  // Iterate over the generated images list and call imageSource
  // to get the image data
  downloadedImages.value = await Promise.all(images.value.map(imageSource))
})

async function imageSource(imageGen) {
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
