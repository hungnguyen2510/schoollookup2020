<template>
  <v-hover>
    <template v-slot:default="{ hover }">
      <v-card class="mx-auto" max-width="344">
        <v-img :src="image"></v-img>
        <v-card-text>
          <h2 class="title primary--text">
            {{ titleCard }}
          </h2>
          Travel to the best outdoor experience on planet Earth. A vacation you
          will never forget!
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
    </template>
  </v-hover>
</template>
<script>
export default {
  data: () => ({
    cardData: [
      {
        titleCard: "Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
        overlay: false
      },
      {
        titleCard: "",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
        overlay: false
      },
      {
        titleCard: "Tìm Hiểu Ngành Nghề",
        image: "https://cdn.vuetifyjs.com/images/cards/forest-art.jpg",
        overlay: false
      }
    ]
  })
};
</script>
