<template>
  <v-row>
    <v-col cols="4">
      <v-select
        :items="dsKhuVuc"
        v-model="selectedKhuVuc"
        label="Chọn Khu Vực"
        item-text="tenkhuvuc"
        item-value="makhuvuc"
        :menu-props="{ maxHeight: '300' }"
        outlined
        @input="GetTruongByKhuVucID"
      ></v-select>
    </v-col>
    <v-col cols="4">
      <v-select
        :items="dsTruong"
        v-model="selectedTruong"
        label="Chọn Trường"
        item-text="tentruong"
        item-value="matruong"
        :menu-props="{ maxHeight: '300' }"
        outlined
        @input="GetThongTinDiemChuanNganh"
      ></v-select>
    </v-col>
    <v-col cols="4">
      <v-select
        :items="dsNamHoc"
        v-model="selectedNamHoc"
        label="Chọn Năm Học"
        item-text="sNamHoc"
        item-value="idNamHoc"
        :menu-props="{ maxHeight: '300' }"
        outlined
      ></v-select>
    </v-col>
    <v-col cols="12">
      <!-- <v-data-table
        :headers="headers"
        :items="dsNganh"
        sort-by="matruong"
        class="elevation-1"
        :loading="!unloading"
        loading-text="Loading... Please wait"
      >
      </v-data-table> -->
    </v-col>
  </v-row>
</template>

<script>
export default {
  data: () => ({
    selectedNamHoc: null,
    selectedKhuVuc: null,
    unloading: false,
    selectedTruong: null,
    dsNganh: [],
    dsNamHoc: [],
    dsKhuVuc: [],
    dsTruong: [],
    // headers: [
    //   {
    //     text: "Mã Trường",
    //     align: "start",
    //     value: "matruong"
    //   }
    // ]
  }),
  async created() {
    this.checkSignIn();
    this.GetKhuVuc();
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
    GetKhuVuc() {
      this.$fire.firestore
        .collection("khuVuc")
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.dsKhuVuc = [...this.dsKhuVuc, { id: doc.id, ...doc.data() }];
            // console.log(this.dsKhuVuc);
          });
        });
    },
    GetTruongByKhuVucID(makhuvuc) {
      this.dsTruong = [];
      if (makhuvuc != 0) {
        this.$fire.firestore
          .collection("truong")
          .where("makhuvuc", "==", makhuvuc)
          .get()
          .then(querySnapshot => {
            // Immutable copy
            querySnapshot.forEach(doc => {
              this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
              //   console.log(this.dsTruong);
            });
          });
      } else {
        this.$fire.firestore
          .collection("truong")
          .get()
          .then(querySnapshot => {
            // Immutable copy
            querySnapshot.forEach(doc => {
              this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
              //   console.log(this.dsTruong);
            });
          });
      }
    },
    GetNamHoc() {
      this.$fire.firestore
        .collection("namhoc")
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.dsNamHoc = [...this.dsNamHoc, { id: doc.id, ...doc.data() }];
            //   console.log(this.dsNamHoc);
          });
        });
    },
    GetThongTinDiemChuanNganh(matruong) {
      this.GetNamHoc();
      this.$fire.firestore
        .collection("truong")
        .where("matruong", "==", matruong)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            // this.dsNamHoc = [...this.dsNamHoc, { id: doc.id, ...doc.data() }];
            console.log(doc.data().manganh);
            this.$fire.firestore
              .collection("nganh")
              .where("manganh", "in", doc.data().manganh)
              .get()
              .then(querySnapshot => {
                // Immutable copy
                querySnapshot.forEach(doc => {
                  this.dsNganh = [
                    ...this.dsNganh,
                    { id: doc.id, ...doc.data() }
                  ];
                  console.log(doc.data());
                  this.unloading = true;
                });
              });
          });
        });
    }
  }
};
</script>

<style></style>
