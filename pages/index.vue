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
      <v-col cols="12" justify="center" align="center">
        <!-- <v-form ref="form" v-model="valid" lazy-validation> -->
        <v-text-field
          v-model="searchText"
          label="Nhập mã ngành hoặc tên ngành mà bạn muốn tìm kiếm"
          @input="timKiemNganh"
          outlined
        ></v-text-field>
        <v-card
          class="mx-auto"
          max-width="500"
          tile
          v-if="dsNganh.length !== 0"
        >
          <v-list flat>
            <v-list-item-group v-model="selectedItem" color="primary">
              <v-list-item v-for="(item, i) in dsNganh" :key="i">
                <v-list-item-icon>
                  <v-icon v-text="item.manganh"></v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title
                    v-text="item.tennganh"
                    @click="showItem(item)"
                  ></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card>
        <!-- </v-form> -->
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
    valid: true,
    selectedItem: null,
    searchText: "",
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
    showItem(item) {
      this.$router.push({
        path: "/search",
        query: { manganh: item.manganh, tennganh: item.tennganh }
      });
    },
    async timKiemNganh(term) {
      if (term != "") {
        const match =
          term.charAt(0).toUpperCase() + term.slice(1).toLowerCase();
        this.dsNganh = [];
        this.isLoading = true;
        const queryByTen = this.$fire.firestore
          .collection("nganh")
          .orderBy("tennganh")
          .startAt(match)
          .endAt(match);
        const queryByMa = this.$fire.firestore
          .collection("nganh")
          .orderBy("manganh")
          .startAt(match)
          .endAt(match);
        const results = await Promise.all([queryByTen.get(), queryByMa.get()]);
        // console.log(results)
        results[0].forEach(doc => {
          this.dsNganh = [...this.dsNganh, { id: doc.id, ...doc.data() }];
          // console.log(doc.data());
        });
        results[1].forEach(doc => {
          this.dsNganh = [...this.dsNganh, { id: doc.id, ...doc.data() }];
          // console.log(doc.data())
        });
        console.log(this.dsNganh);
      } else {
        // this.selectedItem = null;
        this.dsNganh = [];
      }
    }
  }
};
</script>
