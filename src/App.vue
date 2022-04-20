<template>
  <!-- App.vue -->

  <v-app>
    <v-app-bar app>
      <v-toolbar-title>DFU Client</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn rounded="pill">
        Activity
        <v-icon right>mdi-history</v-icon>
      </v-btn>
    </v-app-bar>

    <!-- Sizes your content based upon application components -->
    <v-main>
      <!-- Provides the application the proper gutter -->
      <v-container fluid>
        <v-row dense>
          <v-col cols="12">
            <v-switch label="AUTO" hide-details class="justify-center">
              <template v-slot:prepend>
                <label class="v-label">MANUAL</label>
              </template>
            </v-switch>
          </v-col>
        </v-row>

        <!-- https://vuetifyjs.com/en/components/chips/#custom-list -->
        <v-row>
          <!-- dense -->
          <v-col cols="12" :md="6">
            <v-card class="mx-auto" max-width="600">
              <v-toolbar flat color="transparent">
                <!-- <v-spacer></v-spacer> -->
                <v-select :items="items" label="Select a device"></v-select>
                <!-- <v-spacer></v-spacer> -->
              </v-toolbar>

              <v-card-text class="d-flex justify-space-between"></v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn rounded="pill">Download</v-btn>
                <v-btn rounded="pill">Upload</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>

          <v-col cols="12" :md="6">
            <v-card class="mx-auto" max-width="600">
              <v-toolbar flat color="transparent">
                <!-- <v-spacer></v-spacer> -->
                <v-select :items="items" label="Select an image"></v-select>
                <!-- <v-spacer></v-spacer> -->
              </v-toolbar>

              <v-card-text class="d-flex justify-space-between"></v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn rounded="pill">Upload</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-footer app>
      <!-- -->
    </v-footer>
  </v-app>
</template>

<style>
.v-input__prepend {
  margin-inline-end: unset !important;
  padding-top: unset !important;
}
.v-input__prepend .v-label {
  padding-inline-start: 0px !important;
  padding-inline-end: 10px !important;
}
</style>

 <script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "App",
  data: () => ({
    items: ['Foo', 'Bar', 'Fizz', 'Buzz'],
  }),
});
</script>
