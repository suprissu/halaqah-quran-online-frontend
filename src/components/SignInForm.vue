<template>
  <div>
    <b-form @submit.stop.prevent="onSubmit" autocomplete="off">
      <b-form-group id="input-group-1" label="Username" label-for="input-1">
        <b-form-input
          id="input-1"
          class="custom-input"
          v-model="form.username"
          :state="validateState('username')"
          aria-describedby="input-1-live-feedback"
        ></b-form-input>
        <b-form-invalid-feedback
          id="input-1-live-feedback"
          class="error_username"
        >Username is required</b-form-invalid-feedback>
      </b-form-group>
      <b-form-group id="input-group-2" label="Password" label-for="input-2">
        <b-form-input
          id="input-2"
          class="custom-input"
          v-model="form.password"
          type="password"
          :state="validateState('password')"
          aria-describedby="input-2-live-feedback"
        ></b-form-input>
        <b-form-invalid-feedback
          id="input-2-live-feedback"
          class="error_password"
        >Password is required</b-form-invalid-feedback>
      </b-form-group>
      <div class="btn-container">
        <b-button type="submit" variant="none" ref="btn-submit" class="primary">Login</b-button>
        <p class="mt-4">
          or
          <router-link to="/sign/up">Create New Account</router-link>
        </p>
      </div>
    </b-form>
    <div v-if="isLoading" id="cover-spin"></div>
    <b-modal id="signin-modal" size="sm" hide-header-close centered>
      <div class="modal-img-container">
        <b-img :src="modal.image" class="modal-image" alt="modal-image" fluid />
      </div>
      <div class="modal-align-center">
        <h3>{{ modal.title }}</h3>
        <p>{{ modal.message }}</p>
      </div>
      <template v-slot:modal-footer="{ ok }">
        <b-button
          type="button"
          @click="ok"
          size="sm"
          variant="none"
          class="primary"
          id="modal-button"
        >{{ modal.button }}</b-button>
      </template>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import store from "@/store";
import { required } from "vuelidate/lib/validators";

export default {
  props: {
    inputWidth: Number
  },
  name: "SignInForm",
  data() {
    return {
      isLoading: false,
      show: true,
      form: {
        username: "",
        password: ""
      },
      modal: {
        title: "Invalid Credentials",
        image: require("../assets/img/success-selection.png"),
        message: "No active account found with the given credentials",
        button: "Try Again"
      }
    };
  },
  validations: {
    form: {
      username: {
        required
      },
      password: {
        required
      }
    }
  },
  methods: {
    validateState(name) {
      const { $dirty, $error } = this.$v.form[name];
      return $dirty ? !$error : null;
    },
    postForm() {
      axios
        .post(process.env.VUE_APP_URL + "/api/auth/jwt/create/", {
          username: this.form.username,
          password: this.form.password
        })
        .then(response => {
          store.commit("setRefreshToken", {
            value: response.data.refresh
          });
          store.commit("setAccessToken", {
            value: response.data.access
          });
          axios
            .get(process.env.VUE_APP_URL + "/api/tahfidz/selections/latest/")
            .then(response => {
              store.commit("setSelectionPeriod", {
                value: response.data.latest_opened.id
              });
              window.location.pathname = "/";
            });
        })
        .catch(() => {
          this.$bvModal.show("signin-modal");
        })
        .finally(() => (this.isLoading = false));
    },
    onSubmit() {
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      } else {
        this.isLoading = true;
        this.postForm();
      }
    }
  },
  computed: {
    inputStyle() {
      const halfWidth = this.inputWidth * 0.5;
      return "width: " + halfWidth + "px";
    }
  }
};
</script>

<style lang="scss" scoped>
@import "@/styles/form.scss";
@import "@/styles/reusable/loading.scss";
@import "@/styles/reusable/modal.scss";
</style>