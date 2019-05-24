<template>
  <v-container fill-height>
    <v-layout align-center justify-center>
      <v-flex xs12 sm8 md4>
        <v-card class="elevation-12">
          <v-toolbar dark color="accent">
            <v-toolbar-title>Login Form</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-form ref="form" v-model="valid" lazy-validation @submit.prevent="submit">
              <v-text-field
                v-if="register"
                prepend-icon="person"
                name="name"
                label="Name"
                type="text"
                v-model="name"
                required
              ></v-text-field>
              <v-text-field
                prepend-icon="person"
                name="email"
                label="Email"
                type="email"
                v-model="email"
                :rules="emailRules"
                required
              ></v-text-field>
              <v-text-field
                prepend-icon="lock"
                name="password"
                label="Password"
                id="password"
                type="password"
                required
                v-model="password"
                :rules="passwordRules"
              ></v-text-field>
              <v-card-actions>
                <a
                  v-if="!register"
                  class="caption accent--text ml-3"
                  @click="register=true"
                >register</a>
                <a v-if="register" class="caption accent--text ml-3" @click="register=false">signin</a>
                <v-spacer></v-spacer>
                <v-btn color="primary" :disabled="!valid" type="submit">Login</v-btn>
              </v-card-actions>
            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      register: false,
      valid: false,
      name: "",
      email: "",
      password: "",
      emailRules: [
        v => !!v || "E-mail is required",
        v => /.+@.+/.test(v) || "E-mail must be valid"
      ],
      passwordRules: [
        v => !!v || "Password is required",
        v => v.length >= 5 || "Password must be greater than 6 characters"
      ]
    };
  },
  methods: {
    submit() {
      console.log("line 62");
      if (this.register) {
        this.registerUser();
      } else {
        this.login();
      }
    },
    registerUser() {
      axios({
        url: "http://mongodb+srv://admin:admin@cluster0-d43ve.gcp.mongodb.net/miniWP?retryWrites=true/register",
        method: "POST",
        data: {
          email: this.email,
          password: this.password,
          name: this.name
        }
      })
        .then(user => {
          console.log(user);
          this.login();
        })
        .catch(err => {
          console.log(err);
        });
    },
    login() {
      axios({
        url: "http://mongodb+srv://admin:admin@cluster0-d43ve.gcp.mongodb.net/miniWP?retryWrites=true/signin",
        method: "POST",
        data: {
          email: this.email,
          password: this.password
        }
      })
        .then(user => {
          console.log("then line 72");
          localStorage.setItem("token", user.data.token);
          localStorage.setItem("email", this.email);
          localStorage.setItem("name", user.data.name);
          this.$emit("login");
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>
