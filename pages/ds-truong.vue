<template>
  <v-data-table
    :headers="headers"
    :items="dsTruong"
    sort-by="calories"
    class="elevation-1"
    :loading='!unloading'
    loading-text="Loading... Please wait"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Danh Sách Trường</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>  
      </v-toolbar>
    </template>  
  </v-data-table>
</template>

<script>
export default {
  data: () => ({
    dsTruong: [],
    unloading: false,
    headers: [
      {
        text: "Mã Trường",
        align: "start",
        value: "matruong"
      },
      {
        text: "Tên Trường",
        value: "tentruong"
      },
      {
        text: "Mã Trường",
        align: "end",
        value: "diachi"
      }
    ],
  }),
  async created() {
    this.checkSignIn();
    this.readFromFirestore();
  },
  beforeDestroy() {
    this.removeAuthListener();
  },
  methods: {
    async checkSignIn() {
      this.removeAuthListener = this.$fire.auth.onAuthStateChanged(user => {
        if (user) {
          console.log("Signed in as " + user.email);
        } else {
          this.$router.history.push("/login");
        }
      });
    },
    async readFromFirestore() {
      this.$fire.firestore
        .collection("truong")
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
          });
          this.unloading = true;
          console.log(this.dsTruong);
        });
    }
  }
};
</script>
