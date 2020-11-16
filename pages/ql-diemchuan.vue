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
        @input="GetNamHoc"
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
        @input="GetThongTinDiemChuanNganh"
      ></v-select>
    </v-col>

    <v-col cols="12">
      <v-data-table
        :headers="headers"
        :items="dsNganh"
        sort-by="matruong"
        class="elevation-1"
        :loading="unloading"
        loading-text="Loading... Please wait"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-col cols="12" sm="4" md="2">
              <v-toolbar-title>Danh Sách Trường</v-toolbar-title>
            </v-col>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
          </v-toolbar>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon class="mr-2" color="green" @click="UpdateDiemCuan()">
            mdi-information
          </v-icon>
        </template>
      </v-data-table>
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
    headers: [
      {
        text: "Mã Ngành",
        align: "start",
        value: "manganh"
      },
      {
        text: "Mã Nhóm Ngành",
        value: "manhomnganh"
      },
      {
        text: "Tên Ngành",
        value: "tennganh"
      },
      {
        text: "Điểm Chuẩn",
        value: "diemchuan"
      },
      {
        text: "Actions",
        value: "actions"
      }
    ]
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
      this.dsNamHoc = [];
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
      this.dsNamHoc = [];
      this.$fire.firestore
        .collection("namhoc")
        .orderBy("idNamHoc", "desc")
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.dsNamHoc = [...this.dsNamHoc, { id: doc.id, ...doc.data() }];
            //   console.log(this.dsNamHoc);
          });
        });
    },
    GetThongTinDiemChuanNganh() {
      this.dsNganh = [];
      this.unloading = true;
      let arrtmp = [];
      const arr1 = [];
      this.$fire.firestore
        .collection("truong")
        .where("matruong", "==", this.selectedTruong)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            // console.log(doc.data().manganh);
            arrtmp = doc.data().manganh;
            // arrtmp = doc.data().manganh.splice(10,doc.data().manganh.length);
            // console.log(arrtmp);
            while (arrtmp.length) {
              // console.log(arrtmp.splice(0, 10));
              const tmp = arrtmp.splice(0, 10);
              this.$fire.firestore
                .collection("nganh")
                .where("manganh", "in", tmp)
                .get()
                .then(querySnapshot => {
                  // Immutable copy
                  // console.log(querySnapshot.size,tmp.length)
                  querySnapshot.forEach(doc => {
                    this.dsNganh = [
                      ...this.dsNganh,
                      { id: doc.id, ...doc.data() }
                    ];

                    this.unloading = false;
                  });
                });
            }
            console.log(this.dsNganh);
          });
        });
    },
    UpdateDiemCuan(){
        
    }
  }
};
</script>

<style></style>
