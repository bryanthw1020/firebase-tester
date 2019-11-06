<template>
  <div>
    <v-row>
      <v-col>
        <v-card class="mb-5">
          <v-card-title>Google</v-card-title>
          <v-card-subtitle>Login using google.</v-card-subtitle>

          <div>
            <v-card-text>
              <p class="subheading">Result:</p>
              <v-textarea label="Error Message" :value="googleAuth.errorMessage" outlined disabled />
              <v-textarea label="Returned Token" :value="googleAuth.token" outlined disabled />
            </v-card-text>
          </div>

          <v-card-actions>
            <v-btn
              color="primary"
              :loading="googleAuth.loading"
              :disabled="googleAuth.loading"
              depressed
              @click.prevent="submitGoogleAuth"
            >Login</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col>
        <v-card class="mb-5">
          <v-card-title>Facebook</v-card-title>
          <v-card-subtitle>Login using facebook.</v-card-subtitle>

          <div>
            <v-card-text>
              <p class="subheading">Result:</p>
              <v-textarea
                label="Error Message"
                :value="facebookAuth.errorMessage"
                outlined
                disabled
              />
              <v-textarea label="Returned Token" :value="facebookAuth.token" outlined disabled />
            </v-card-text>
          </div>

          <v-card-actions>
            <v-btn
              color="primary"
              :loading="facebookAuth.loading"
              :disabled="facebookAuth.loading"
              depressed
              @click.prevent="submitFacebookAuth"
            >Login</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col>
        <v-card class="mb-5">
          <v-card-title>Anonymous</v-card-title>
          <v-card-subtitle>Login as anonymous user.</v-card-subtitle>

          <div>
            <v-card-text>
              <p class="subheading">Result:</p>
              <v-textarea
                label="Error Message"
                :value="anonymousAuth.errorMessage"
                outlined
                disabled
              />
              <v-textarea label="Returned Token" :value="anonymousAuth.token" outlined disabled />
            </v-card-text>
          </div>

          <v-card-actions>
            <v-btn
              color="primary"
              :loading="anonymousAuth.loading"
              :disabled="anonymousAuth.loading"
              depressed
              @click.prevent="submitAnonymousAuth"
            >Login</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <v-row>
      <v-col>
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
      </v-col>
      <v-col>
        <v-card class="mb-5">
          <v-card-title>Mobile</v-card-title>
          <v-card-subtitle>Login using mobile number.</v-card-subtitle>
          <div>
            <v-card-text>
              <template v-if="mobileAuth.step == 1">
                <v-text-field label="Phone No" v-model="mobileAuth.phone_no"></v-text-field>
              </template>
              <template v-else-if="mobileAuth.step==2">
                <v-text-field label="Verification Code" v-model="mobileAuth.verification_code"></v-text-field>
              </template>
            </v-card-text>

            <v-card-text>
              <p class="subheading">Result:</p>
              <v-textarea label="Error Message" :value="mobileAuth.errorMessage" outlined disabled />
              <v-textarea label="Returned Token" :value="mobileAuth.token" outlined disabled />
            </v-card-text>
          </div>
          <v-card-actions>
            <template v-if="mobileAuth.step == 1">
              <v-btn
                id="get-verification-code"
                color="primary"
                :loading="mobileAuth.loading"
                :disabled="mobileAuth.loading"
                depressed
              >Get Verification Code</v-btn>
            </template>
            <template v-else-if="mobileAuth.step==2">
              <v-btn
                color="primary"
                :loading="mobileAuth.loading"
                :disabled="mobileAuth.loading"
                depressed
                @click.prevent="submitMobileAuth"
              >Submit</v-btn>
            </template>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import firebase from "~/services/firebase";
import { auth, GoogleProvider, FacebookProvider } from "~/services/firebase";

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
        step: 1,
        loading: false,
        phone_no: null,
        verification_code: null,
        errorMessage: null,
        token: null
      },
      googleAuth: {
        loading: false,
        errorMessage: null,
        token: null
      },
      facebookAuth: {
        loading: false,
        errorMessage: null,
        token: null
      },
      anonymousAuth: {
        loading: false,
        errorMessage: null,
        token: null
      }
    };
  },
  methods: {
    async submitEmailAuth() {
      if (this.emailAuth.loading === false) {
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
      try {
        if (!this.mobileAuth.verification_code) {
          this.mobileAuth.errorMessage = "Please enter verification code.";
          return;
        }

        let result = await window.confirmationResult.confirm(
          this.mobileAuth.verification_code
        );
        this.mobileAuth.token = await result.user.getIdToken();
        this.resetMobileLogin();
      } catch (err) {
        this.mobileAuth.errorMessage = err.message || null;
      } finally {
        this.mobileAuth.loading = false;
      }
    },
    async submitGoogleAuth() {
      try {
        this.googleAuth.loading = true;
        let creds = await auth.signInWithPopup(GoogleProvider);
        this.googleAuth.token = await creds.user.getIdToken();
      } catch (err) {
        this.googleAuth.errorMessage = err.message || null;
      } finally {
        this.googleAuth.loading = false;
      }
    },
    async submitFacebookAuth() {
      try {
        this.facebookAuth.loading = true;
        let creds = await auth.signInWithPopup(FacebookProvider);
        this.facebookAuth.token = await creds.user.getIdToken();
      } catch (err) {
        this.facebookAuth.errorMessage = err.message || null;
      } finally {
        this.facebookAuth.loading = false;
      }
    },
    async submitAnonymousAuth() {
      try {
        this.anonymousAuth.loading = true;
        let creds = await auth.signInAnonymously();
        this.anonymousAuth.token = await creds.user.getIdToken();
      } catch (err) {
        this.anonymousAuth.errorMessage = err.message || null;
      } finally {
        this.anonymousAuth.loading = false;
      }
    },
    async getVerificationCode() {
      try {
        if (!this.mobileAuth.phone_no) {
          this.mobileAuth.errorMessage = "Please enter phone no.";
          return;
        }

        let appVerifier = window.recaptchaVerifier;
        let confirmationResult = await auth.signInWithPhoneNumber(
          this.mobileAuth.phone_no,
          appVerifier
        );

        this.mobileAuth.step++;
        window.confirmationResult = confirmationResult;
      } catch (err) {
        this.mobileAuth.errorMessage = err.message || null;
      } finally {
        this.mobileAuth.loading = false;
        grecaptcha.reset(window.recaptchaWidgetId);
      }
    },
    resetMobileLogin() {
      this.mobileAuth.step = 1;
      this.mobileAuth.phone_no = this.mobileAuth.verification_code = null;
      grecaptcha.reset(window.recaptchaWidgetId);
    }
  },
  mounted() {
    const self = this;

    window.recaptchaVerifier = new firebase.auth.RecaptchaVerifier(
      "get-verification-code",
      {
        size: "invisible",
        callback: function(response) {
          self.getVerificationCode();
        }
      }
    );

    window.recaptchaVerifier.render().then(widgetId => {
      window.recaptchaWidgetId = widgetId;
    });
  }
};
</script>