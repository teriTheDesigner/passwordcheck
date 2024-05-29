<template>
  <div class="step3">
    <div class="headline">
      <h1>Validation Check</h1>
      <p>
        In this component we use packages to validate all fields and show the
        strength of password.
      </p>
    </div>
    <form id="form" @submit.prevent="nextStep">
      <div>
        <div>
          <label for="first_name">
            First Name
            <span>*</span></label
          >
          <input
            required
            v-model="form.first_name"
            type="text"
            name="first_name"
            id="first_name"
            placeholder=""
          />
        </div>
        <div>
          <label for="last_name" c>
            Last Name
            <span>*</span></label
          >
          <input
            required
            v-model="form.last_name"
            type="text"
            name="last_name"
            id="last_name"
            placeholder=""
          />
        </div>
        <div class="col-span-12">
          <label for="email">
            Email
            <span>*</span></label
          >
          <input
            required
            v-model="form.email"
            type="email"
            name="email"
            id="email"
            placeholder=""
          />
          <span class="error" v-if="v$.form.email.$error">
            {{ v$.form.email.$errors[0].$message }}
          </span>
        </div>
        <div>
          <PasswordCheck
            v-model:password="form.password"
            :errors="passwordErrors"
          />
          <!-- <span class="text-red-700" v-if="errors.password">{{
                    errors.password
                  }}</span> -->
        </div>
      </div>
      <div>
        <button v-if="!loading" @click="validateForm">Continue</button>
        <button v-else disabled>Loading</button>
      </div>
    </form>
  </div>
</template>

<script>
import PasswordCheck from "./components/PasswordCheck.vue";
import useValidate from "@vuelidate/core";
import { required, email, minLength, helpers } from "@vuelidate/validators";
export default {
  components: {
    PasswordCheck,
  },
  props: ["errors"],

  data() {
    return {
      passwordErrors: [],
      loading: false,
      form: {
        first_name: "",
        last_name: "",
        email: "",
        password: "",
      },
    };
  },
  validations() {
    const containsCapitalLetter = (value) => /[A-Z]/.test(value);
    const containsNumber = (value) => /\d/.test(value);
    const containsSpecialCharacter = (value) =>
      /[!@#$%^&*(),.?":{}|<>]/.test(value);

    return {
      form: {
        first_name: { required },
        last_name: { required },
        email: { required, email },
        password: {
          required,
          minLength: minLength(8),
          containsCapitalLetter: helpers.withMessage(
            "Value needs to include at least 1 capital letter",
            containsCapitalLetter
          ),
          containsNumber: helpers.withMessage(
            "Value needs to include at least 1 number",
            containsNumber
          ),
          containsSpecialCharacter: helpers.withMessage(
            "Value needs to include at least 1 special character",
            containsSpecialCharacter
          ),
        },
      },
    };
  },

  setup() {
    const v$ = useValidate();
    return { v$ };
  },
  methods: {
    validateForm() {
      this.v$.$validate();
      if (!this.v$.$error) {
        console.log("successful");
      } else {
        if (this.v$.form.password.$error) {
          console.log(this.v$.form.password.$errors, "password error message");
          this.passwordErrors = this.v$.form.password.$errors.map(
            (error) => error.$message
          );
        } else {
          console.log(this.v$.$errors[0].$message, "error message");
        }
      }
    },
    nextStep() {
      if (this.step === 2) {
        this.createNewSystem();
      } else {
        this.step++;
      }
    },
    previousStep() {
      if (this.step <= 0) {
      } else {
        this.step--;
      }
    },
    createNewSystem() {
      this.loading = true;

      if (
        this.v$.form.first_name.$error ||
        this.v$.form.last_name.$error ||
        this.v$.form.email.$error ||
        this.v$.form.password.$error
      ) {
        this.step = 2;
      }
    },
  },
};
</script>
