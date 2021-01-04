<template>
  <v-row>
    <v-card
      class="mx-auto"
      max-width="400"
      v-for="(item, index) in cardData"
      v-bind:key="index"
    >
      <v-img class="align-end" height="200px" width="500px">
        <v-card-title>{{ item.tennhomnganh }}</v-card-title>
      </v-img>

      <v-card-subtitle>
        {{ item.manhomnganh }}
      </v-card-subtitle>

      <v-card-text class="text--primary">
        <div>Whitehaven Beach</div>

        <div>Whitsunday Island, Whitsunday Islands</div>
      </v-card-text>

      <v-card-actions>
        <v-btn color="orange" text>
          Xem Thêm
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-row>
</template>
<script>
export default {
  data: () => ({
    cardData: []
  }),
  async created() {
    await this.GetNhomNganh();
    // console.log(this.cardData)
    //  v-bind:src="cardData[index].image"
  },
  methods: {
    GetNhomNganh() {
      return this.$fire.firestore
        .collection("nhomnganh")
        .limit(4)
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            this.cardData = [...this.cardData, { id: doc.id, ...doc.data() }];
          });
        });
    }
  }
};
</script>
