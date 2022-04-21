<template>
  <!-- App.vue -->

  <v-app>
    <v-app-bar app>
      <v-toolbar-title>DFU Client</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn rounded="pill">
        <!-- v-model="activity" -->
        Activity
        <v-icon right>mdi-history</v-icon>
      </v-btn>
    </v-app-bar>

    <!-- Sizes your content based upon application components -->
    <v-main>
      <!-- Provides the application the proper gutter -->
      <v-container fluid>
        <v-row dense justify="center" class="pa-3">
          <v-chip-group v-model="mode" column mandatory variant="outlined">
            <v-chip size="x-large" filter filter-icon="mdi-cog-outline" value="manual">
              <span class="text-uppercase">Manual</span>
            </v-chip>
            <v-chip size="x-large" filter filter-icon="mdi-refresh-auto" value="auto">
              <span class="text-uppercase">Auto</span>
            </v-chip>
          </v-chip-group>
        </v-row>

        <!-- https://vuetifyjs.com/en/components/chips/#custom-list -->
        <v-row>
          <!-- dense -->
          <v-col cols="12" :md="6">
            <v-card class="mx-auto" max-width="600">
              <v-toolbar flat color="transparent">
                <v-select variant="outlined" :items="items" label="Device selection"></v-select>
              </v-toolbar>

              <v-card-text>
                <v-container class="pa-0">
                  <v-row>
                    <v-col cols="12" sm="6">
                      <v-select variant="outlined" hide-details label="Alternate setting"></v-select>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-select variant="outlined" hide-details label="Memory address"></v-select>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-chip-group v-model="operation" column mandatory variant="outlined">
                  <v-chip filter filter-icon="mdi-download-outline" value="download">
                    <span class="text-uppercase">Download</span>
                  </v-chip>
                  <v-chip filter filter-icon="mdi-upload-outline" value="upload">
                    <span class="text-uppercase">Upload</span>
                  </v-chip>
                </v-chip-group>
              </v-card-actions>
            </v-card>
          </v-col>

          <v-col cols="12" :md="6">
            <v-card class="mx-auto" max-width="600">
              <v-toolbar flat color="transparent">
                <v-select
                  v-if="operation === 'download'"
                  variant="outlined"
                  :items="items"
                  label="Image source"
                ></v-select>
                <v-text-field v-else variant="outlined" label="Image target"></v-text-field>
              </v-toolbar>

              <v-card-text class="d-flex justify-space-between"></v-card-text>

              <v-card-actions>
                <v-file-input
                  variant="outlined"
                  accept="*.bin"
                  label="Select from your computer..."
                ></v-file-input>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>

        <v-row justify="center" class="pa-3">
          <v-dialog v-model="execute">
            <template v-slot:activator="{ props }">
              <v-btn rounded="pill" variant="outlined" size="large" v-bind="props">Execute</v-btn>
            </template>
            <v-card>
              <v-card-title>Executing request...</v-card-title>
              <v-divider></v-divider>
              <v-card-text style="font-family:Consolas,Monaco,Lucida Console,Liberation Mono,DejaVu Sans Mono,Bitstream Vera Sans Mono,Courier New,monospace;">
                  Opening DFU capable USB device...
                  ID 0483:df11
                  Run-time device DFU version 011a
                  Claiming USB DFU Interface...
                  Setting Alternate Setting #0 ...
                  Determining device status: state = dfuIDLE, status = 0
                  dfuIDLE, continuing
                  DFU mode device DFU version 011a
                  Device returned transfer size 2048
                  DfuSe interface name: "Internal Flash "
                  Downloading to address = 0x08000000, size = 26400
                  Download [=========================] 100% 26400 bytes
                  Download done.
                  File downloaded successfully
              </v-card-text>
              <v-divider></v-divider>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-chip
                  variant="outlined"
                  size="large"
                  class="text-uppercase"
                  @click="execute = false"
                >Close</v-chip>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-row>
      </v-container>
    </v-main>

    <v-footer app>
      <!-- -->
    </v-footer>
  </v-app>
</template>

<style>
.v-card-actions {
  padding-left: 1rem !important;
  padding-right: 1rem !important;
}
</style>

 <script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "App",
  data: () => ({
    items: ['Foo', 'Bar', 'Fizz', 'Buzz'],
    operation: 'upload',
    mode: 'manual',
    execute: false,
  }),
  methods: {
    async upload_image(): Promise<void> {
      const response = await fetch("localhost:8000/images", {
        method: "POST",
        mode: "cors", // cross-domain request
        body: JSON.stringify({

        })
      })
    }
  }
});
</script>
