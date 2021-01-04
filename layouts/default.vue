<template>
  <v-app dark>
    <v-navigation-drawer
      permanent
      floating
      class="blue-grey darken-3"
      expand-on-hover
      app
    >
      <v-list>
        <v-list-item to="/">
          <v-list-item-icon>
            <v-icon class="white--text">mdi-home</v-icon>
          </v-list-item-icon>

          <v-list-item-title class="white--text">Home</v-list-item-title>
        </v-list-item>

        <v-list-group no-action>
          <v-icon slot="prependIcon" color="white">mdi-account-circle</v-icon>
          <template v-slot:activator>
            <v-list-item-title class="white--text">Danh Sách</v-list-item-title>
          </template>
          <v-list-item
            class="white--text"
            v-for="(item, i) in danhsach"
            :key="i"
            :to="item.to"
            router
            exact
          >
            <v-list-item-action>
              <v-icon class="white--text">{{ item.icon }}</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title class="white--text" v-text="item.title" />
            </v-list-item-content>
          </v-list-item>
        </v-list-group>
        <v-list-group no-action v-if="showMenuAdmin">
          <v-icon slot="prependIcon" color="white">mdi-account-circle</v-icon>
          <template v-slot:activator>
            <v-list-item-content>
              <v-list-item-title class="white--text">Admin</v-list-item-title>
            </v-list-item-content>
          </template>

          <v-list-item v-for="(item, i) in admins" :key="i" :to="item.to">
            <v-list-item-icon>
              <v-icon v-text="item.icon" class="white--text"></v-icon>
            </v-list-item-icon>
            <v-list-item-title
              class="white--text"
              v-text="item.title"
            ></v-list-item-title>
          </v-list-item>
          <v-btn @click="SignOut()">Sign out</v-btn>
        </v-list-group>
      </v-list>
    </v-navigation-drawer>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>

<script>
import Footer from "~/components/Footer.vue";
import swal from "sweetalert";
export default {
  data: () => ({
    clipped: false,
    drawer: false,
    fixed: false,
    title: "School Lookup",
    showMenuAdmin: false,
    danhsach: [
      {
        icon: "mdi-apps",
        title: "DS Trường",
        to: "/ds-truong"
      },
      { icon: "mdi-apps", title: "DS Nhóm Ngành", to: "/ds-nhomnganh" }
    ],
    admins: [
      {
        icon: "mdi-apps",
        title: "Điểm Chuẩn",
        to: "/ql-diemchuan"
      },
      { icon: "mdi-apps", title: "Import", to: "/import" }
    ]
  }),
  components: {
    Footer
  },
  async created() {
    this.checkSignIn();
  },
  beforeDestroy() {
    this.removeAuthListener();
  },
  methods: {
    async checkSignIn() {
      this.removeAuthListener = this.$fire.auth.onAuthStateChanged(user => {
        if (user) {
          this.showMenuAdmin = true;
          swal("Đăng nhập thành công!", user.email, "success");
          this.$router.history.push("/");
        }
        else{
          this.showMenuAdmin = false;
          console.log("Chua dang nhap")
        }
      });
    },
    SignOut() {
      this.$fire.auth
        .signOut()
        .then(() => {
          console.log("Sign-out successful");
        })
        .catch(() => {
          console.log("Sign-out failed");
        });
    }
  }
};
</script>
