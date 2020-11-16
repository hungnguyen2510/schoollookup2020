<template>
  <v-container>
    <v-row>
      <v-col cols="3">
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
      <v-col cols="3">
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
      <v-col cols="3">
        <v-select
          :items="dsNamHoc"
          v-model="selectedNamHoc"
          label="Chọn Năm Học"
          item-text="sNamHoc"
          item-value="idNamHoc"
          :menu-props="{ maxHeight: '300' }"
          outlined
          @input="GetNganh"
        ></v-select>
      </v-col>
      <v-col cols="3">
        <v-select
          :items="dsNganh"
          v-model="selectedNganhHoc"
          label="Chọn Ngành Học"
          item-text="tennganh"
          item-value="manganh"
          :menu-props="{ maxHeight: '300' }"
          outlined
          @input="GetDiemChuan"
        ></v-select>
      </v-col>

      <v-col cols="12">
        <v-data-table
          :headers="headers"
          :items="dsDiemChuan"
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
              <v-dialog v-model="dialogInsert" max-width="500px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    color="primary"
                    dark
                    class="mb-2"
                    v-bind="attrs"
                    v-on="on"
                  >
                    New Item
                  </v-btn>
                </template>
              </v-dialog>
            </v-toolbar>
          </template>

          <template v-slot:[`item.actions`]="{ item }">
            <v-icon class="mr-2" color="green" @click="UpdateDiemChuan(item)">
              mdi-information
            </v-icon>
          </template>
        </v-data-table>
      </v-col>
    </v-row>
    <v-dialog v-model="dialogInsert" max-width="700px" persistent>
      <v-card>
        <v-card-title>
          <span class="headline">Thêm môn học</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
              v-model="selectedNamHoc"
              label="Năm Học"
              outlined
              disabled
            ></v-text-field>
            <v-row>
              <v-col cols="12" sm="6" md="3">
                <v-text-field
                  v-model="editedItem.manganh"
                  label="Mã Ngành"
                  outlined
                  disabled
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="2">
                <v-text-field
                  v-model="editedItem.manhomnganh"
                  label="Mã Nhóm Ngành"
                  outlined
                  disabled
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="7">
                <v-text-field
                  v-model="editedItem.tennganh"
                  label="Tên Ngành"
                  outlined
                  disabled
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="8">
                <v-text-field
                  v-model="editedItem.diemchuan"
                  label="Điểm Chuẩn"
                  outlined
                  clearable
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="error" text @click="close"> Hủy </v-btn>
          <v-btn color="blue darken-1" text @click="save"> Thêm </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar v-model="snackbar" :timeout="timeout" right top>
      {{ errText }}
      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false"
          >Đóng</v-btn
        >
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    valid: false,
    snackbar: false,
    errText: "",
    dialogInsert: false,
    timeout: 2000,
    selectedNamHoc: null,
    selectedKhuVuc: null,
    selectedNganhHoc: null,
    unloading: false,
    selectedTruong: null,
    dsNganh: [],
    dsNamHoc: [],
    dsKhuVuc: [],
    dsTruong: [],
    editedIndex: -1,
    dsDiemChuan: [],
    editedItem: {
      manganh: "",
      manhomnganh: "",
      tennhomnganh: "",
      diemchuan: ""
    },
    headers: [
      {
        text: "Khối",
        value: "khoi"
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
  watch: {
    selectedKhuVuc() {
      (this.dsNganh = []),
        (this.selectedNganhHoc = null),
        (this.dsNamHoc = []),
        (this.selectedNamHoc = null),
        (this.dsTruong = []);
      this.selectedTruong = null;
    },
    selectedTruong() {
      (this.dsNamHoc = []),
        (this.selectedNamHoc = null),
        (this.dsNganh = []),
        (this.selectedNganhHoc = null);
    },
    selectedNamHoc() {
      (this.dsNganh = []), (this.selectedNganhHoc = null);
    }
  },
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
    GetNganh() {
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
          });
        });
    },
    GetDiemChuan() {
      console.log(
        this.selectedNamHoc,
        this.selectedNganhHoc,
        this.selectedTruong
      );
    },
    UpdateDiemChuan(item) {
      this.dialogInsert = true;
      console.log(item);
      this.editedIndex = this.dsNganh.indexOf(item);
      this.editedItem = Object.assign({}, item);
    },
    close() {
      this.dialogInsert = false;
    },
    save() {
      // console.log(this.selectedTruong, this.)
    }
  }
};
</script>

<style></style>
