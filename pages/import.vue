<template>
  <v-container>
    <v-toolbar>
      <v-tabs dark background-color="primary" grow>
        <v-tab v-model="tab" @click="changeTab(1)">
          Import Trường
        </v-tab>

        <v-tab v-model="tab" @click="changeTab(2)">
          Import Ngành
        </v-tab>

        <v-tab v-model="tab" @click="changeTab(3)">
          Import Điểm
        </v-tab>
      </v-tabs>
    </v-toolbar>
    <v-spacer></v-spacer>
    <!-- <ImportTruong /> -->
    <div v-if="tab==1">
        <ImportTruong />
    </div>
    <div v-else-if="tab==2">
        fdsfgdgs
    </div>
  </v-container>
</template>

<script>
import ImportTruong from "../components/importTruong";
export default {
  data: () => ({
    tab: 1
  }),
  components: {
    ImportTruong
  },
  methods: {
    changeTab(item) {
      this.tab = item;
      console.log(this.tab);
    }
  }
};
</script>

<style></style>
