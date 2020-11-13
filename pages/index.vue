<template>
  <div class="">
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
      <v-col cols="12" sm="8" md="6">
        <!-- <CardHome/> -->
      </v-col>
    </v-row>
  </div>
</template>

<script>
import Logo from "~/components/Logo.vue";
import VuetifyLogo from "~/components/VuetifyLogo.vue";
import CardHome from "~/components/CardHome.vue";

export default {
  data: () => ({
    search:"",
    dsNganh: [],
    isLoading: false
  }),
  components: {
    Logo,
    CardHome,
    VuetifyLogo
  },
  methods: {
    async timKiemNganh(term){
      this.dsTruong = [];
      this.isLoading = true;
      const queryByTen = this.$fire.firestore.collection("nganh").orderBy('tennganh').startAt(term).endAt(term + '~');
      const queryByMa = this.$fire.firestore.collection("nganh").orderBy('manganh').startAt(term).endAt(term + '~');
      const results = await Promise.all([queryByTen.get(), queryByMa.get()]);
      results[0].forEach(doc => {
        this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
      });
      results[1].forEach(doc => {
        this.dsTruong = [...this.dsTruong, { id: doc.id, ...doc.data() }];
      }); 
      console.log(this.dsTruong);
    }
  }
};
</script>
