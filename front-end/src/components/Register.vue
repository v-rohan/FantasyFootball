<template>
  <div v-if="loading">
    <img class="image" src="../../public/favicon.svg" alt width="120" height="120" />
  </div>
  <div v-else>
    <v-app id="config">
      <span class="bg"></span>
      <v-content>
        <v-card class="mx-auto overflow-hidden">
          <v-app-bar color="red ">
            <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>
            <v-spacer />
            <center>
              <v-toolbar-title>
                <strong>Fantasy Football 2020</strong>
              </v-toolbar-title>
            </center>
            <v-spacer />
          </v-app-bar>
        </v-card>

        <v-navigation-drawer v-model="drawer" absolute temporary>
          <v-list nav dense>
            <v-list-item-group active-class="deep-purple--text text--lighten-2">
              <router-link tag="span" to="/">
                <v-list-item>
                  <v-list-item-icon>
                    <v-icon>mdi-home</v-icon>
                  </v-list-item-icon>
                  <v-list-item-title>Home</v-list-item-title>
                </v-list-item>
              </router-link>
              <router-link tag="span" to="/players">
                <v-list-item>
                  <v-list-item-icon>
                    <v-icon>mdi-soccer</v-icon>
                  </v-list-item-icon>
                  <v-list-item-title>CONTINUE AUCTION</v-list-item-title>
                </v-list-item>
              </router-link>
            </v-list-item-group>
          </v-list>
        </v-navigation-drawer>
        <v-container absolute fluid>
          <v-row align="center" justify="center">
            <v-col cols="12" sm="8" md="4">
              <v-card class="elevation-12">
                <v-toolbar color="light-green darken-1" dark flat>
                  <v-spacer></v-spacer>
                  <v-toolbar-title>Register Teams</v-toolbar-title>
                  <v-spacer />
                </v-toolbar>
                <v-card-text>
                  <v-form>
                    <v-text-field v-model="name" label="Team Name" name="name" type="text" />

                    <v-text-field v-model="money" label="Base Price" name="money" type="text" />
                  </v-form>
                </v-card-text>
                <v-card-actions>
                  <v-spacer />
                  <v-btn @click="register" color="primary">Register</v-btn>
                  <v-btn @click="auction" color="red darken-4">START</v-btn>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-content>
      <Footer />
    </v-app>
  </div>
</template>

<script>
import axios from "axios";
import Footer from "./layout/Footer";

export default {
  name: "Register",
  components: {
    Footer
  },
  data() {
    return {
      drawer: false,

      team: "",
      money: "",
      error: "",
      loading: true
    };
  },
  created() {
    this.$vuetify.theme.dark = true;
    this.sleep(500).then(() => {
      this.loading = false;
    });
  },
  methods: {
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },
    register() {
      const team = {
        name: this.name,
        money: this.money
      };
      axios.post("http://localhost:3000/addTeam", team).then(
        res => {
          if (res.status === 200) {
            if (res.data.done === false) alert(`ERROR! ${res.data.message}`);
            else {
              alert(`Sucesss! ${res.data.message}`);
              location.reload();
            }
          }
        },
        err => {
          console.log(err.response);
          this.error = err.response.data.error;
        }
      );
    },
    auction() {
      axios.post("http://localhost:3000/loadCsv").then(() => {
        this.$router.push("/players");
      });
    }
  }
};
</script>


<style scoped>
.bg {
  width: 100%;
  height: 90%;
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0.4;
  background: url("../../img/bg1.png") no-repeat;
  background-size: cover;
  background-color: blue;
  transform: scale(1.1);
}
#inspire {
  height: 100%;
  width: 100%;
  overflow: auto;
  padding-right: 20px;
}

.image {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 120px;
  height: 120px;
  margin: -60px 0 0 -60px;
  webkit-animation: spin 4s linear infinite;
  -moz-animation: spin 4s linear infi-nite;
  animation: spin 4s linear infinite;
}
@-moz-keyframes spin {
  100% {
    -moz-transform: rotate(360deg);
  }
}
@-webkit-keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
  }
}
@keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
</style>
