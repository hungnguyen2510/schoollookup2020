<template>
  <v-row>
    <v-card
      class="mx-auto"
      max-width="400"
      v-for="item in cardData"
      v-bind:key="item.id"
    >
      <v-img
        class="white--text align-end"
        height="200px"
        v-bind:src="item.image"
      >
        <v-card-title>{{item.titleCard}}</v-card-title>
      </v-img>

      <v-card-subtitle class="pb-0">
        Number 10
      </v-card-subtitle>

      <v-card-text class="text--primary">
        <div>Whitehaven Beach</div>

        <div>Whitsunday Island, Whitsunday Islands</div>
      </v-card-text>

      <v-card-actions>
        <v-btn color="orange" text>
          Share
        </v-btn>

        <v-btn color="orange" text>
          Explore
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-row>
</template>
<script>
export default {
  data: () => ({
    cardData: [
      {
        id: 1,
        titleCard: "Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
      },
      {
        id: 2,
        titleCard: "Tìm Hiểu Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
      },
      {
        id: 3,
        titleCard: "Tìm Hiểu Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
      },{
        id: 4,
        titleCard: "Tìm Hiểu Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
      }
    ]
  }),
  created() {
    this.GetNhomNganh()
  },
  methods:{
     GetNhomNganh() {
      this.$fire.firestore
        .collection("nhomnganh")
        .get()
        .then(querySnapshot => {
          // Immutable copy
          querySnapshot.forEach(doc => {
            // this.dsNganh = [...this.dsNganh, { id: doc.id, ...doc.data() }];
            console.log(doc.data());
          });
          // this.unloading = true;
          // console.log(this.dsNganh);
        });
    }
  }
};
</script>
