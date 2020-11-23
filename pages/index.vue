<template>
  <v-container>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="8" md="6">
        <div class="text-center">
          <logo />
          <vuetify-logo />
        </div>
      </v-col>
    </v-row>
    <v-row justify="center" align="center">
      <v-col cols="12">
        <v-text-field
          label="Nhập mã ngành hoặc tên ngành mà bạn muốn tìm kiếm"
          @input="timKiemNganh"
          outlined
        ></v-text-field>
      </v-col>
    </v-row>
    <CardHome />
  </v-container>
</template>

<script>
import Logo from "~/components/Logo.vue";
import VuetifyLogo from "~/components/VuetifyLogo.vue";
import CardHome from "~/components/CardHome.vue";

export default {
  data: () => ({
    search: "",
    dsNganh: [],
    isLoading: false,
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
  }),
  components: {
    Logo,
    CardHome,
    VuetifyLogo
  },
  methods: {
    async timKiemNganh(term) {
      this.dsTruong = [];
      this.isLoading = true;
      const queryByTen = this.$fire.firestore
        .collection("nganh")
        .orderBy("tennganh")
        .startAt(term)
        .endAt(term + "~");
      const queryByMa = this.$fire.firestore
        .collection("nganh")
        .orderBy("manganh")
        .startAt(term)
        .endAt(term + "~");
      const results = await Promise.all([queryByTen.get(), queryByMa.get()]);
      // console.log(results)
      results[0].forEach(doc => {
        this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
        console.log(doc.data())
      });
      results[1].forEach(doc => {
        this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
        // console.log(doc.data())
      });
      console.log(this.dsTruong);
    }
  }
};
</script>
