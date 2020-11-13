<template>
  <v-hover>
    <template v-slot:default="{ hover }">
      <v-layout v-for="item in cardData" v-bind:key="item.id">
        <v-card
          class="mx-auto"
          max-width="344"
          v-for="item in cardData"
          v-bind:key="item.id"
        >
          <v-img :src="item.image"></v-img>
          <v-card-text>
            <h2 class="title primary--text">
              {{ item.titleCard }}
            </h2>
            Travel to the best outdoor experience on planet Earth. A vacation
            you will never forget!
          </v-card-text>
          <v-card-title>
            <v-rating
              :value="4"
              dense
              color="orange"
              background-color="orange"
              hover
              class="mr-2"
            ></v-rating>
            <span class="primary--text subtitle-2">64 Reviews</span>
          </v-card-title>
          <v-fade-transition>
            <v-overlay v-if="hover" absolute color="#036358">
              <v-btn>See more info</v-btn>
            </v-overlay>
          </v-fade-transition>
        </v-card>
      </v-layout>
    </template>
  </v-hover>
</template>
<script>
export default {
  data: () => ({
    cardData: [
      {
        id: 1,
        titleCard: "Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
        overlay: false
      },
      {
        id: 2,
        titleCard: "",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
        overlay: false
      },
      {
        id: 3,
        titleCard: "Tìm Hiểu Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
        overlay: false
      }
    ]
  })
};
</script>
