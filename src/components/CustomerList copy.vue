<template>
  <div>
    <v-data-table :headers="headers" :items="customers" class="elevation-1">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Listado clientes</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer />
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                Nuevo cliente
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="newItem.name"
                        label="Name"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancelar
                </v-btn>
                <v-btn color="blue darken-1" text @click="save">Guardar</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>

      <!-- <template v-slot:item.actions="{ item }"> -->
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="verClientes(item)">
          mdi-pencil
        </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="load">Reset</v-btn>
      </template>
    </v-data-table>
    <v-dialog v-model="dialogClientes" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Clientes</span>
        </v-card-title>
        <v-card-text>
          <v-container> </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="closeDialogoClientes">
            Cancelar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "CustomerList",
  mounted() {
    this.load();
  },
  methods: {
    async load() {
      console.log("cargando..");
      await axios
        .get("https://testingweb.free.beeceptor.com/customers")
        .then((response) => {
          if (response.data) {
            this.customers = response.data;
          } else {
            console.log("Error cargando datos...");
          }
        });
    },

    verClientes(item) {
      const editedIndex = this.customers.indexOf(item);
      console.log(editedIndex);
      let id = this.customers[editedIndex].id;
      console.log("cargando sus clientes..");
      axios
        .get(`https://testingweb.free.beeceptor.com/customers/${id}/users`)
        .then((response) => {
          if (response.data) {
            this.susClientes = response.data;
            this.dialogClientes = true;
          } else {
            console.log("Error cargando datos...");
          }
        });

      this.dialog = true;
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDialogoClientes() {
      this.dialogClientes = false;
    },

    async save() {
      const newCustomer = {
        id: Math.random(), //prueba quitar
        name: this.newItem.name,
      };

      console.log(JSON.stringify(newCustomer));

      await axios
        .post(
          "https://testingweb.free.beeceptor.com/customers",
          JSON.stringify(newCustomer)
        )
        .then((response) => {
          if (response.data) {
            //this.load();  cuando funcione el insertar nuevo cliente, se descomentaria
            this.customers.push(newCustomer); //esta linea se comentaria cuando funcione
          }
        })
        .catch((error) => {
          // this.load();
          this.customers.push(newCustomer);
          console.error(error);
        });
      this.close();
    },
  },
  data: () => ({
    dialog: false,
    dialogClientes: false,
    headers: [
      {
        text: "ID",
        align: "start",
        value: "id",
      },
      { text: "Name", value: "name" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    customers: [],
    editedIndex: -1,
    newItem: {
      name: "",
    },
    susClientes: [],
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
  },
};
</script>
