<template>
  <v-app dark>
    <v-navigation-drawer permanent
        expand-on-hover app>
      <v-list>
        <v-list-item to="/">
          <v-list-item-icon>
            <v-icon>mdi-home</v-icon>
          </v-list-item-icon>

          <v-list-item-title>Home</v-list-item-title>
        </v-list-item>

        <v-list-group no-action prepend-icon="mdi-account-circle">
          <template v-slot:activator>
            <v-list-item-title>Danh Sách</v-list-item-title>
          </template>
          <v-list-item
            v-for="(item, i) in danhsach"
            :key="i"
            :to="item.to"
            router
            exact
          >
            <v-list-item-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title v-text="item.title" />
            </v-list-item-content>
          </v-list-item>
        </v-list-group>
        <v-list-group no-action prepend-icon="mdi-account-circle">
          <template v-slot:activator>
            <v-list-item-content>
              <v-list-item-title>Actions</v-list-item-title>
            </v-list-item-content>
          </template>

          <v-list-item v-for="(item, i) in admins" :key="i" :to="item.to">
            <v-list-item-icon>
              <v-icon v-text="item.icon"></v-icon>
            </v-list-item-icon>
            <v-list-item-title v-text="item.title"></v-list-item-title>
          </v-list-item>
        </v-list-group>
      </v-list>
    </v-navigation-drawer>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <Footer/>
  </v-app>
</template>

<script>
import Footer from "~/components/Footer.vue";
export default {
  data: () => ({
    clipped: false,
    drawer: false,
    user:false,
    fixed: false,
    title: "School Lookup",
    danhsach: [
      {
        icon: "mdi-apps",
        title: "DS Trường",
        to: "/ds-truong"
      },
      { icon: "mdi-apps", title: "DS Ngành", to: "/ds-nganh" }
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
  }
};
</script>
