<template>
  <v-row class="d-flex flex-row-reverse">
    <v-dialog v-model="dialog" persistent max-width="600px">
      <template v-slot:activator="{ on, attrs }">
        <v-btn color="primary" rounded dark v-bind="attrs" v-on="on">
          Crear Usuario
          <v-icon>
            mdi-plus
          </v-icon>
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Crear nuevo usuario</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  label="Username*"
                  v-model="username"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  label="Email*"
                  v-model="email"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  label="Password*"
                  type="password"
                  v-model="password"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6">
                <v-select
                  :items="['admin', 'editores']"
                  label="Rol*"
                  v-model="rol"
                  required
                ></v-select>
              </v-col>
            </v-row>
          </v-container>
          <small>*el campo es obligatorio</small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="red darken-1"
            text
            v-on:click="cleanDialog(), (dialog = false)"
          >
            Cancelar
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            v-on:click="createUser(), (dialog = false)"
          >
            Confirmar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import { Auth, API } from "aws-amplify";
export default {
  data: () => ({
    dialog: false,
    username: "",
    email: "",
    password: "",
    rol: "",
  }),
  methods: {
    createUser: async function() {
      let init = {
        body: {
          username: this.username,
          groupname: this.rol,
        },
        headers: {
          "Content-Type": "application/json",
          Authorization: `${(await Auth.currentSession())
            .getAccessToken()
            .getJwtToken()}`,
        },
      };
      try {
        await Auth.signUp({
          username: this.username,
          password: this.password,
          attributes: {
            email: this.email,
          },
        });
        await API.post("AdminQueries", "/addUserToGroup", init);
      } catch (error) {
        console.error("Error signing up: ", error);
      }
    },
    cleanDialog: function() {
      this.username = "";
      this.email = "";
      this.password = "";
      this.rol = "";
    },
  },
};
</script>

<style></style>
