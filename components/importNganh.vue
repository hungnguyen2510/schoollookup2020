<template>
  <v-container fluid>
    <v-file-input
      v-model="fileExcel"
      color="deep-purple accent-4"
      counter
      label="File excel input"
      multiple
      placeholder="Select your files"
      prepend-icon="mdi-paperclip"
      outlined
      @change="handleLoadExcel"
      :show-size="1000"
    >
      <template v-slot:selection="{ index, text }">
        <v-chip v-if="index < 2" color="deep-purple accent-4" dark label small>
          {{ text }}
        </v-chip>

        <span
          v-else-if="index === 2"
          class="overline grey--text text--darken-3 mx-2"
        >
          +{{ files.length - 2 }} File(s)
        </span>
      </template>
    </v-file-input>
    <v-data-table :items="dataImport" :headers="headers" class="elevation-1">
      <template v-slot:top>
        <v-toolbar flat>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="headline"
                >Bạn Có Muốn Xóa Trường Này Không?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialog" max-width="800px">
            <v-card>
              <v-card-title>
                <span class="headline">Edit</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.manganh"
                        label="Mã ngành"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="12">
                      <v-text-field
                        v-model="editedItem.tennganh"
                        label="Tên ngành"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.manhomnganh"
                        label="Mã nhóm ngành"
                      ></v-text-field>
                    </v-col>                 
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save">
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:[`item.manganh`]="{ item }">
        <v-chip color="green" dark>
          {{ item.manganh }}
        </v-chip>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon class="mr-2" @click="editItem(item)" color="orange">
          mdi-pencil
        </v-icon>
        <v-icon @click="deleteItem(item)" color="red">
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>
    <!-- <v-dialog v-model="dialogShowInsert" max-width="700px"> -->
      <!-- <template v-slot:activator="{ on, attrs }">
        <v-btn
          v-bind="attrs"
          v-on="on"
          color="primary"
          fab
          rounded
          class="mt-3"
          :disabled="dataImport.length === 0"
          @click="InsertTruong"
        >
          <v-icon>mdi-plus</v-icon>
        </v-btn>
      </template> -->
    <!-- </v-dialog> -->
    <div class="text-center mt-4">
      <v-btn
        :loading="loading"
        :disabled="loading || fileExcel == null"
        color="green"
        class="white--text"
        @click="UploadFile()"
      >
        Upload Server
        <v-icon right dark>
          mdi-cloud-upload
        </v-icon>
      </v-btn>
    </div>
  </v-container>
</template>

<script>
import XLSX from "xlsx";
export default {
  data() {
    return {
      loader: null,
      dialog: false,
      dialogDelete: false,
      dialogShowInsert: false,
      loading: false,
      fileExcel: null,
      dataImport: [],
      editedIndex: -1,
      editedItem: {
        manganh: "",
        tennganh: "",
        manhomnganh: "",
      },
      defaultItem: {
        manganh: "",
        tennganh: "",
        manhomnganh: "",
      },
      headers: [
        {
          text: "Mã Ngành",
          value: "manganh"
        },
        {
          text: "Tên Ngành",
          value: "tennganh"
        },
        {
          text: "Mã Nhóm Ngành",
          value: "manhomnganh"
        },
        { text: "Actions", value: "actions", sortable: false }
      ]
    };
  },
  watch: {
    dialogDelete(val) {
      val || this.closeDelete();
    }
  },
  methods: {
    handleLoadExcel() {
      this.loader = "loading";
      const files = this.fileExcel;
      if (!files.length) {
        return;
      } else if (!/\.(xls|xlsx)$/.test(files[0].name.toLowerCase())) {
        this.fileExcel = null;
        return alert(
          "The upload format is incorrect. Please upload xls or xlsx format"
        );
      }
      const reader = new FileReader();
      if (reader.readAsBinaryString) {
        reader.onload = e => {
          const workbook = XLSX.read(reader.result, { type: "binary" });
          const firstSheet = workbook.SheetNames[0];
          const excelRows = XLSX.utils.sheet_to_row_object_array(
            workbook.Sheets[firstSheet]
          );
          this.dataImport = excelRows;
          this.loading = false;
        };
        reader.readAsBinaryString(files[0]);
      }
    },
    // InsertTruong() {
    //   if (this.dataImport.length === 0) return;
    //   const lastIDItem = this.dataImport.length + 1;
    //   console.log(lastIDItem);
    // },

    UploadFile() {
      console.log(this.dataImport);
    },

    editItem(item) {
      console.log(item);
      this.editedIndex = this.dataImport.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.dataImport.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.dataImport.splice(this.editedIndex, 1);
      console.log(this.dataImport);
      this.closeDelete();
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.dataImport[this.editedIndex], this.editedItem);
      } else {
        this.dataImport.push(this.editedItem);
      }
      this.close();
    }
  }
};
</script>

<style>
.custom-loader {
  animation: loader 1s infinite;
  display: flex;
}
@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-o-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
