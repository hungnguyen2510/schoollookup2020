<template>
  <v-container>
    <v-list flat>
      <v-list-item-group color="primary">
        <v-list-item v-for="(item, i) in dsTruong" :key="i">
          <v-list-item-content>
            <v-list-item-title
              v-text="item.tentruong"
            ></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    manganh: "",
    tennganh: "",
    dsTruong: []
  }),
  async created() {
    this.manganh = await this.$route.query.manganh;
    this.tennganh = await this.$route.query.tennganh;
    console.log(this.manganh, this.tennganh);
    this.timKiemNganh({ manganh: this.manganh, tennganh: this.tennganh });
  },
  methods: {
    async timKiemNganh(result) {
      //   console.log([result.manganh]);
      this.$fire.firestore
        .collection("truong")
        .where("manganh", "array-contains", result.manganh)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
            // console.log(doc.data());
          });
          this.unloading = true;
          console.log(this.dsTruong);
        });
    }
  }
};
</script>

<style></style>
