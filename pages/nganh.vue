<template>
  <v-container>
    <h3>Nhóm ngành: {{ tennhomnganh }}</h3>
    <v-data-table :items="dsNganh" :headers="headerNganh" item-key="manganh">
    </v-data-table>
    <h3>Một số trường đào tạo các ngành của nhóm ngành {{ tennhomnganh }}</h3>
    <v-data-table :items="dsTruong" :headers="headerTruong" item-key="matruong">
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon class="mr-2" @click="inforTruong(item)" color="green">
          mdi-information
        </v-icon>
      </template>
    </v-data-table>
    <v-dialog v-if="itemTruong.length > 0" v-model="dialog" width="800">
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
            <strong>Địa Chỉ: </strong> {{ itemTruong[0]["diachi"] }}
          </v-card-text>
          <v-card-text>
            <strong>Số Điện Thoại: </strong> {{ itemTruong[0]["sdt"] }}
          </v-card-text>
          <v-card-text>
            <strong>Website: </strong><a>{{ itemTruong[0]["website"] }}</a>
          </v-card-text>
          <v-card-text>
            <strong>Email: </strong> {{ itemTruong[0]["email"] }}
          </v-card-text>
          <v-card-text>
            <strong>Ngành đào tạo: </strong>
            <v-data-table
              :items="itemNganhByTruong"
              :headers="headerNganh"
              item-key="manganh"
            >
              <template v-slot:[`item.actions`]="{ item }">
                <v-icon
                  class="mr-2"
                  @click="inforDiemChuan(item)"
                  color="green"
                >
                  mdi-information
                </v-icon>
              </template>
            </v-data-table>
          </v-card-text>
        </div>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    manganh: "",
    show: false,
    tennhomnganh: "",
    dsNganh: [],
    dsTruong: [],
    itemTruong: [],
    itemNganhByTruong: [],
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
      }
    ],
    headerTruong: [
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
        text: "Thông Tin Thêm",
        value: "actions"
      }
    ]
  }),
  async created() {
    this.manganh = await this.$route.query.manganh;
    this.tennhomnganh = await this.$route.query.tennhomnganh;
    this.timKiemNganh({
      manganh: this.manganh,
      tennhomnganh: this.tennhomnganh
    });
  },
  methods: {
    async timKiemNganh(result) {
      result.manganh.map(mn => {
        this.$fire.firestore
          .collection("nganh")
          .where("manganh", "==", mn)
          .get()
          .then(querySnapshot => {
            // Immutable copy
            querySnapshot.forEach(doc => {
              this.dsNganh = [...this.dsNganh, { id: doc.id, ...doc.data() }];
              // console.log(doc.data())
            });
            this.unloading = true;
          });
      });
      // console.log(result.manganh)
      this.$fire.firestore
        .collection("truong")
        .where("manganh", "array-contains-any", result.manganh)
        .get()
        .then(querySnapshot => {
          querySnapshot.forEach(doc => {
            this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
          });
        });
    },
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
          this.unloading = true;
        });
      let arrtmp = [];
      const arr1 = [];
      arrtmp = manganh;
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
            // console.log("this.itemNganhByTruong", this.itemNganhByTruong);
          });
      }
    }
  }
};
</script>

<style></style>
