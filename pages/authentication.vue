<template>
  <div>
    <v-card class="mb-5">
      <v-card-title>Email</v-card-title>
      <v-card-subtitle>Login using email and password.</v-card-subtitle>
      <div>
        <v-card-text>
          <v-text-field label="Email" v-model="emailAuth.email"></v-text-field>
          <v-text-field type="password" label="Password" v-model="emailAuth.password"></v-text-field>
        </v-card-text>

        <v-card-text>
          <p class="subheading">Result:</p>
          <v-textarea label="Error Message" :value="emailAuth.errorMessage" outlined disabled />
          <v-textarea label="Returned Token" :value="emailAuth.token" outlined disabled />
        </v-card-text>
      </div>
      <v-card-actions>
        <v-btn
          color="primary"
          :loading="emailAuth.loading"
          :disabled="emailAuth.loading"
          depressed
          @click.prevent="submitEmailAuth"
        >Submit</v-btn>
      </v-card-actions>
    </v-card>

    <v-card class="mb-5">
      <v-card-title>Mobile</v-card-title>
      <v-card-subtitle>Login using mobile number.</v-card-subtitle>
      <div>
        <v-card-text>
          <v-text-field label="Phone No" v-model="mobileAuth.phone_no"></v-text-field>
          <v-text-field label="Verification Code" v-model="mobileAuth.verification_code"></v-text-field>
        </v-card-text>

        <v-card-text>
          <p class="subheading">Result:</p>
          <v-textarea label="Error Message" :value="mobileAuth.errorMessage" outlined disabled />
          <v-textarea label="Returned Token" :value="mobileAuth.token" outlined disabled />
        </v-card-text>
      </div>
      <v-card-actions>
        <v-btn
          color="primary"
          :loading="mobileAuth.loading"
          :disabled="mobileAuth.loading"
          depressed
          @click.prevent="submitMobileAuth"
        >Submit</v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import { auth } from "~/services/firebase";

export default {
  data() {
    return {
      emailAuth: {
        loading: false,
        email: null,
        password: null,
        errorMessage: null,
        token: null
      },
      mobileAuth: {
        loading: false,
        phone_no: null,
        verification_code: null,
        errorMessage: null,
        token: null
      }
    };
  },
  methods: {
    async submitEmailAuth() {
      if (this.emailAuth.loading === false) {
        console.log("signing in");

        try {
          this.emailAuth.loading = true;
          this.emailAuth.token = this.emailAuth.errorMessage = null;
          let creds = await auth.signInWithEmailAndPassword(
            this.emailAuth.email,
            this.emailAuth.password
          );
          this.emailAuth.token = await creds.user.getIdToken();
        } catch (err) {
          this.emailAuth.errorMessage = err.message || null;
        } finally {
          this.emailAuth.loading = false;
        }
      }
    },
    async submitMobileAuth() {
      //
    }
  }
};
</script>