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
            <v-chip
              size="x-large"
              filter
              filter-icon="mdi-cog-outline"
              value="manual"
            >
              <span class="text-uppercase">Manual</span>
            </v-chip>
            <v-chip
              size="x-large"
              filter
              filter-icon="mdi-refresh-auto"
              value="auto"
            >
              <span class="text-uppercase">Auto</span>
            </v-chip>
          </v-chip-group>
        </v-row>

        <!-- https://vuetifyjs.com/en/components/chips/#custom-list -->
        <v-row>
          <!-- dense -->
          <v-col cols="12" :md="6" @mouseenter="fetchDevices()">
            <v-card class="mx-auto" max-width="600">
              <v-toolbar flat color="transparent">
                <v-select
                  variant="outlined"
                  :items="serials"
                  v-model="selected_serial"
                  label="Device selection"
                ></v-select>
              </v-toolbar>

              <v-card-text>
                <v-container class="pa-0">
                  <v-row>
                    <v-col cols="12" sm="6">
                      <v-select
                        variant="outlined"
                        hide-details
                        label="Alternate setting"
                        :items="alt_settings"
                        v-model="selected_alt"
                      ></v-select>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-select
                        variant="outlined"
                        hide-details
                        label="Memory address"
                        :items="addresses"
                        v-model="selected_address"
                      ></v-select>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-chip-group
                  v-model="operation"
                  column
                  mandatory
                  variant="outlined"
                >
                  <v-chip
                    filter
                    filter-icon="mdi-download-outline"
                    value="download"
                  >
                    <span class="text-uppercase">Download</span>
                  </v-chip>
                  <v-chip
                    filter
                    filter-icon="mdi-upload-outline"
                    value="upload"
                  >
                    <span class="text-uppercase">Upload</span>
                  </v-chip>
                </v-chip-group>
              </v-card-actions>
            </v-card>
          </v-col>

          <v-col cols="12" :md="6" @mouseenter="fetchImages()">
            <v-card class="mx-auto" max-width="600">
              <v-toolbar flat color="transparent">
                <v-select
                  v-if="operation === 'download'"
                  variant="outlined"
                  label="Image source"
                  :items="filenames"
                  v-model="selected_filename"
                ></v-select>
                <v-text-field
                  v-else
                  variant="outlined"
                  label="Image target"
                ></v-text-field>
              </v-toolbar>

              <v-card-text class="d-flex justify-space-between"></v-card-text>

              <v-card-actions>
                <v-file-input
                  @change="onFileChange"
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
              <v-btn
                rounded="pill"
                variant="outlined"
                size="large"
                v-bind="props"
                >Execute</v-btn
              >
            </template>
            <v-card>
              <v-card-title>Executing request...</v-card-title>
              <v-divider></v-divider>
              <v-card-text
                style="
                  font-family: Consolas, Monaco, Lucida Console, Liberation Mono,
                    DejaVu Sans Mono, Bitstream Vera Sans Mono, Courier New,
                    monospace;
                "
              >
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
                  >Close</v-chip
                >
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-row>
      </v-container>
    </v-main>

    <!-- <v-footer app>
      Images : {{ images }} <br />
      Devices: {{ devices }} <br />
      Serials: {{ serials }} <br />
      Alternate settings: {{ alt_settings }} <br/>
      Addresses : {{ addresses }} <br/>
    </v-footer> -->
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
    operation: "upload",
    mode: "manual",
    execute: false,
    files: [],
    images: [],

    devices: [],
    selected_device: null,

    // serials: [],
    selected_serial: "319235713237",

    // alt_settings: [],
    selected_alt: "Internal Flash",

    // addresses: [],
    selected_address: "0x08000000",

    // filenames: [],
    selected_filename: "blink_rate.NUCLEO_F401RE.bin",
  }),

  computed: {
    serials: function () {
      return this.devices.map((device) => device.serial);
    },
    alt_settings: function () {
      for (const device of this.devices) {
        if (device.serial == this.selected_serial) {
          return device.alt.map((alt) => alt.name);
        }
      }
    },
    addresses: function () {
      for (const device of this.devices) {
        if (device.serial == this.selected_serial) {
          for (const alt of device.alt) {
            if (alt.name == this.selected_alt ) {
              return alt.sectors.map((sector) => sector.address);
            }
          }
        }
      }
    },
  },

  methods: {
    async fetchImages() {
      const response = await fetch("http://localhost:8000/images", {
        method: "GET",
        mode: "cors",
      });
      this.images = await response.json();
    },

    async fetchDevices() {
      const response = await fetch("http://localhost:8000/devices", {
        method: "GET",
        mode: "cors",
      });
      this.devices = await response.json();
    },

    onFileChange(e) {
      this.files = e.target.files;
      this.upload_image();
    },

    async upload_image(): Promise<void> {
      const formData = new FormData();
      formData.append("upload_file", this.files[0]);

      const response = await fetch("http://localhost:8000/images", {
        method: "POST",
        mode: "cors", // cross-domain request
        body: formData,
      });
    },
  },
});
</script>
