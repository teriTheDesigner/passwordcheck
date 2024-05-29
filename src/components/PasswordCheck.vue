<template>
  <div>
    <label for="password">
      Password
      <span>*</span></label
    >
    <input
      required
      v-model="password"
      type="password"
      @input="updatePassword"
      name="password"
      id="password"
      placeholder=""
    />
    <password-meter :password="password" />
    <span v-if="errors && errors.length > 0">
      <ul>
        <li class="error" v-for="error in errors" :key="error">{{ error }}</li>
      </ul>
    </span>
    <!-- <span class="text-red-700" v-if="errors.password">{{
        errors.password
      }}</span> -->
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import PasswordMeter from "vue-simple-password-meter";

export default defineComponent({
  components: {
    PasswordMeter,
  },
  props: {
    errors: {
      default: () => [],
    },
  },
  setup(_, { emit }) {
    const password = ref("");

    const updatePassword = (event) => {
      emit("update:password", event.target.value);
    };

    return {
      password,
      updatePassword,
    };
  },
});
</script>
