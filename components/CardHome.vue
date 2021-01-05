<template>
  <v-row>
    <v-card
      class="mx-auto"
      max-width="400"
      v-for="(item, index) in itemNhomNganh"
      v-bind:key="index"
    >
      <v-img class="align-end" height="200px" width="500px">
        <v-card-title>{{ item.tennhomnganh }}</v-card-title>
      </v-img>

      <v-rating
        v-model="item.rating"
        color="yellow darken-3"
        background-color="grey darken-1"
        empty-icon="$ratingFull"
        half-increments
        hover
        large
      ></v-rating>

      <v-card-actions>
        <v-btn color="orange" text @click="handleNhomNganh(item)">
          Xem Thêm
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-row>
</template>
<script>
export default {
  data: () => ({
    itemNhomNganh: [],
  }),
  async created() {
    await this.GetNhomNganh();
  },
  methods: {
    GetNhomNganh() {
      return this.$fire.firestore
        .collection("nhomnganh2020")
        .limit(4)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.itemNhomNganh = [
              ...this.itemNhomNganh,
              { id: doc.id, ...doc.data() }
            ];
          });
          // console.log(this.itemNhomNganh)
        });
    },
    handleNhomNganh(item) {
      // console.log(nhomnganh);
      this.$router.push({
        path: "/nganh",
        query: { manganh: item.manganh, tennhomnganh: item.tennhomnganh }
      });
    }
  }
};
</script>
