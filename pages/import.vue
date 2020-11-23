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
    <div v-if="tab == 1">
      <ImportTruong />
    </div>
    <div v-else-if="tab == 2">
      <ImportNganh />
    </div>
    <div v-else-if="tab == 3">
      <ImportDiem />
    </div>
  </v-container>
</template>

<script>
import ImportTruong from "../components/importTruong";
import ImportNganh from "../components/importNganh";
import ImportDiem from "../components/importDiem";
export default {
  data: () => ({
    tab: 1
  }),
  components: {
    ImportTruong,
    ImportNganh,
    ImportDiem
  },
  created() {
    this.checkSignIn();
  },
  methods: {
    changeTab(item) {
      this.tab = item;
    },
    async checkSignIn() {
      this.removeAuthListener = this.$fire.auth.onAuthStateChanged(user => {
        if (user) {
          // console.log("Signed in as " + user.email);
        } else {
          this.$router.history.push("/login");
        }
      });
    }
  }
};
</script>

<style></style>
