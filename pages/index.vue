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
          outlined
        ></v-text-field>
      </v-col>
      <v-col cols="12" sm="8" md="6">
        <CardHome/>
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
    title:""
  }),
  components: {
    Logo,
    CardHome,
    VuetifyLogo
  }
};
</script>
