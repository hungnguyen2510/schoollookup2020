<template>
  <v-container>
      <h3>Ngành {{tennganh}}</h3>
    <v-card v-if="itemTruong.length > 0">
      <v-img
        src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
        height="200px"
      ></v-img>

      <v-card-title>
        {{ itemTruong[0].tentruong }}
      </v-card-title>

      <v-card-subtitle class="subtitle-1">
        {{ itemTruong[0].matruong }}
      </v-card-subtitle>

      <v-card-actions>
        <v-btn color="orange lighten-2" @click="show = !show" text>
          Xem thêm
        </v-btn>

        <v-spacer></v-spacer>

        <v-btn icon @click="show = !show">
          <v-icon>{{ show ? "mdi-chevron-up" : "mdi-chevron-down" }}</v-icon>
        </v-btn>
      </v-card-actions>

      <!-- <v-expand-transition> -->
      <div v-show="show">
        <v-divider></v-divider>
        <v-card-text>
          <strong>Địa Chỉ: </strong> {{ itemTruong[0].diachi }}
        </v-card-text>
        <v-card-text>
          <strong>Số Điện Thoại: </strong> {{ itemTruong[0].sdt }}
        </v-card-text>
        <v-card-text>
          <strong>Website: </strong><a>{{ itemTruong[0].website }}</a>
        </v-card-text>
        <v-card-text>
          <strong>Email: </strong> {{ itemTruong[0].email }}
        </v-card-text>
        <v-card-text>
          <strong>Ngành đào tạo: </strong>
          <v-data-table
            :items="itemNganhByTruong"
            :headers="headerNganh"
            :hide-default-footer="true"
            item-key="manganh"
          >
            <template v-slot:[`item.actions`]="{ item }">
              <v-icon class="mr-2" @click="inforDiemChuan(item)" color="green">
                mdi-information
              </v-icon>
            </template>
          </v-data-table>
        </v-card-text>
      </div>
    </v-card>
    <v-dialog v-model="dialogDiemChuan" width="500">
      <v-card>
        <v-card-text>
          <v-data-table
            :items="itemDiemChuanByKhoi"
            :headers="headerDiemChuan"
            item-key="makhoi"
            :hide-default-footer="true"
          >
          </v-data-table>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    matruong: "",
    dialogDiemChuan: false,
    show: true,
    manganh: "",
    tennganh: "",
    dsNganh: [],
    dsTruong: [],
    itemTruong: [],
    itemNganhByTruong: [],
    itemDiemChuanByKhoi: [],
    headerNganh: [
      {
        text: "Mã Ngành",
        align: "start",
        value: "manganh"
      },
      {
        text: "Tên Ngành",
        value: "tennganh"
      },
      {
        text: "Khối",
        value: "khoi"
      },
      {
        text: "Thông Tin Thêm",
        value: "actions"
      }
    ],
    headerDiemChuan: [
      {
        text: "Mã Khối",
        align: "start",
        value: "makhoi"
      },
      {
        text: "Điểm Chuẩn",
        value: "diemchuan"
      }
    ]
  }),
  async created() {
    this.matruong = await this.$route.query.matruong;
    this.manganh = await this.$route.query.manganh;
    this.tennganh = await this.$route.query.tennganh;
    this.inforTruong({
      matruong: this.matruong,
      manganh: this.manganh
    });
  },
  methods: {
    inforTruong({ matruong, manganh }) {
      this.itemNganhByTruong = [];
      this.dialog = true;
      this.itemTruong = [];
      this.$fire.firestore
        .collection("truong")
        .where("matruong", "==", matruong)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.itemTruong = [
              ...this.itemTruong,
              { id: doc.id, ...doc.data() }
            ];
          });
          //   console.log(this.itemTruong)
          this.unloading = true;
          let arrtmp = [];
          const arr1 = [];
          arrtmp = this.itemTruong[0].manganh;
          while (arrtmp.length) {
            const tmp = arrtmp.splice(0, 10);
            this.$fire.firestore
              .collection("nganh")
              .where("manganh", "in", tmp)
              .get()
              .then(querySnapshot => {
                // Immutable copy
                querySnapshot.forEach(doc => {
                  this.itemNganhByTruong = [
                    ...this.itemNganhByTruong,
                    { id: doc.id, ...doc.data() }
                  ];
                });
                this.unloading = true;
              });
          }
        });
    },
    inforDiemChuan({ manganh }) {
      // console.log(this.itemTruong[0].matruong);
      this.itemDiemChuanByKhoi = [];
      this.dialogDiemChuan = true;
      this.$fire.firestore
        .collection("diemchuan")
        .where("matruong", "==", this.itemTruong[0].matruong)
        .where("manganh", "==", manganh)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.itemDiemChuanByKhoi = [
              ...this.itemDiemChuanByKhoi,
              { id: doc.id, ...doc.data() }
            ];
            // console.log(doc.data());
          });
          // console.log(this.itemDiemChuanByKhoi);
        });
    }
  }
};
</script>

<style></style>
