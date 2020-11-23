<template>
  <v-row justify="center">
    <v-col cols="12" sm="10" md="8" lg="6">
      <v-card ref="form">
        <v-card-text>
          <v-text-field
            v-model="email"
            :rules="[() => !!email || 'Vui lòng nhập thông tin']"
            label="Email"
            placeholder="Nhập email"
            suffix="@gmail.com"
            required
          ></v-text-field>
          <v-text-field
            v-model="password"
            :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
            :type="show ? 'text' : 'password'"
            label="Password"
            placeholder="Nhập Password"
            required
            :rules="[() => !!password || 'Vui lòng nhập thông tin']"
            @click:append="show = !show"
          ></v-text-field>
        </v-card-text>
        <v-divider class="mt-12"></v-divider>
        <v-card-actions>
          <v-btn text>
            Cancel
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="Login">
            Login
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import swal from "sweetalert";
export default {
  data: () => ({
    email: null,
    password: null,
    show: false
  }),
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
          swal("Đăng nhập thành công!", user.email, "success");
          this.$router.history.push("/");
        }
      });
    },
    async Login() {
      await this.$fire.auth
        .createUserWithEmailAndPassword(
          this.email + "@gmail.com",
          this.password
        )
        .then(item => {})
        .catch(err => {
          if (err.code == "auth/email-already-in-use") {
            this.$fire.auth
              .signInWithEmailAndPassword(
                this.email + "@gmail.com",
                this.password
              )
          }
          if (err.code == "auth/weak-password") {
            swal("Mật khẩu phải trên 6 kí tự", "", "warning");
          }
        });
    }
  }
};
</script>

<style></style>
