<template>
  <v-container>
    <h3>Nhóm ngành: {{ tennhomnganh }}</h3>
    <v-data-table :items="dsNganh" :headers="headerNganh" item-key="manganh">
    </v-data-table>
    <h3>Một số trường đào tạo các ngành của nhóm ngành {{ tennhomnganh }}</h3>
    <v-data-table :items="dsTruong" :headers="headerTruong" item-key="matruong">
    </v-data-table>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    manganh: "",
    tennhomnganh: "",
    dsNganh: [],
    dsTruong: [],
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
            this.dsTruong = [...this.dsTruong, {id: doc.id, ...doc.data()}]
          });
        });
    }
  }
};
</script>

<style></style>
