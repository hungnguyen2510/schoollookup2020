<template>
  <v-container>
    <v-data-table
      :headers="headers"
      :items="dsTruong"
      sort-by="matruong"
      class="elevation-1"
      :loading="!unloading"
      loading-text="Loading... Please wait"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-col cols="12" sm="4" md="2">
            <v-toolbar-title>Danh Sách Trường</v-toolbar-title>
          </v-col>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-col class="mt-8" cols="12" sm="4" md="2">
            <v-select
              v-model="selectedKhuVuc"
              :items="dsKhuVuc"
              item-text="tenkhuvuc"
              item-value="makhuvuc"
              label="Chọn Khu Vực"
              dense
              outlined
              width="50px"
              @input="FilterKhuVuc"
            ></v-select>
          </v-col>
        </v-toolbar>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon class="mr-2" @click="inforTruong(item)" color="green">
          mdi-information
        </v-icon>
      </template>
    </v-data-table>
    <v-dialog v-if="itemTruong.length > 0" v-model="dialog" width="500">
      <v-card>
        <v-img
          src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
          height="200px"
        ></v-img>

        <v-card-title>
          {{ itemTruong[0]["tentruong"] }}
        </v-card-title>

        <v-card-subtitle class="subtitle-1">
          {{ itemTruong[0]["matruong"] }}
        </v-card-subtitle>

        <v-card-actions>
          <v-btn color="orange lighten-2" text>
            Xem thêm
          </v-btn>

          <v-spacer></v-spacer>

          <v-btn icon @click="show = !show">
            <v-icon>{{ show ? "mdi-chevron-up" : "mdi-chevron-down" }}</v-icon>
          </v-btn>
        </v-card-actions>

        <v-expand-transition>
          <div v-show="show">
            <v-divider></v-divider>
            <v-card-text>
              <strong>Địa Chỉ:</strong> {{ itemTruong[0]["diachi"] }}
            </v-card-text>
            <v-card-text>
              <strong>Số Điện Thoại:</strong> {{ itemTruong[0]["sdt"] }}
            </v-card-text>
            <v-card-text>
              <strong>Website:</strong> {{ itemTruong[0]["website"] }}
            </v-card-text>
            <v-card-text>
              <strong>Email:</strong> {{ itemTruong[0]["email"] }}
            </v-card-text>
          </div>
        </v-expand-transition>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    show: false,
    dialog: false,
    selectedKhuVuc: null,
    itemTruong: [],
    dsTruong: [],
    dsKhuVuc: [],
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
    },
    GetKhuVuc() {
      this.$fire.firestore
        .collection("khuVuc")
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.dsKhuVuc = [...this.dsKhuVuc, { id: doc.id, ...doc.data() }];
            console.log(this.dsKhuVuc);
          });
        });
    },
    inforTruong({ id, matruong }) {
      this.dialog = true;
      this.itemTruong = [];
      this.$fire.firestore
        .collection("truong")
        .where("id", "==", id)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.itemTruong = [
              ...this.itemTruong,
              { id: doc.id, ...doc.data() }
            ];
            console.log(this.itemTruong);
          });
          this.unloading = true;
          // console.log(this.dsTruong);
        });
    },
    FilterKhuVuc(maKhuVuc) {
      this.dsTruong = [];
      if (maKhuVuc == 0) {
        this.$fire.firestore
          .collection("truong")
          .get()
          .then(querySnapshot => {
            // Immutable copy
            querySnapshot.forEach(doc => {
              this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
              // console.log(doc.data());
            });
            this.unloading = true;
            // console.log(this.dsTruong);
          });
      } else {
        this.$fire.firestore
          .collection("truong")
          .where("makhuvuc", "==", maKhuVuc)
          .get()
          .then(querySnapshot => {
            // Immutable copy
            querySnapshot.forEach(doc => {
              this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
              // console.log(doc.data());
            });
            this.unloading = true;
            // console.log(this.dsTruong);
          });
      }
    }
  }
};
</script>
