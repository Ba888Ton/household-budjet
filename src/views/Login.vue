<template>
  <form class="card auth-card" @submit.prevent="submitHandler">
    <div class="card-content">
      <span class="card-title">Домашняя бухгалтерия</span>
      <div class="input-field">
        <input
          id="email"
          type="text"
          v-model.trim="email"
          :class="{
            invalid:
              ($v.email.$dirty && !$v.email.required) ||
              ($v.email.$dirty && !$v.email.email),
          }"
        />
        <label for="email">Email</label>
        <small
          v-if="$v.email.$dirty && !$v.email.required"
          class="helper-text invalid"
          >Поле email не должно быть пустым
        </small>
        <small
          v-else-if="$v.email.$dirty && !$v.email.email"
          class="helper-text invalid"
          >Поле email ведено некорректно
        </small>
      </div>
      <div class="input-field">
        <input
          id="password"
          type="password"
          v-model.trim="password"
          :class="{ invalid: 
            ($v.password.$dirty && !$v.password.required) ||
            ($v.password.$dirty && !$v.password.minLength) }"
        />
        <label for="password">Пароль</label>
        <small
          v-if="$v.password.$dirty && !$v.password.required"
          class="helper-text invalid"
          >Поле пароль не должно быть пустым
        </small>
        <small
          v-else-if="$v.password.$dirty && !$v.password.minLength"
          class="helper-text invalid"
          >Введите не меньше {{this.$v.password.$params.minLength.min}}-ми символов. Сейчас он {{ password.length }}
        </small>
      </div>
    </div>
    <div class="card-action">
      <div>
        <button class="btn waves-effect waves-light auth-submit" type="submit">
          Войти
          <i class="material-icons right">send</i>
        </button>
      </div>

      <p class="center">
        Нет аккаунта?
        <router-link to="/register">Зарегистрироваться</router-link>
      </p>
    </div>
  </form>
</template>

<script>
import { email, required, minLength } from "vuelidate/lib/validators";
import messages from '@/utils/messages'
export default {
  name: "login",
  data: () => ({
    email: "",
    password: "",
  }),
  validations: {
    email: { required, email },
    password: { required, minLength: minLength(8) },
  },
  mounted () {
    if (messages[this.$route.query.message]) {
      this.$message(messages[this.$route.query.message])
    }
  },
  methods: {
    async submitHandler() {
      if (this.$v.$invalid) {
        this.$v.$touch();
        return;
      }
      const formData = {
        email: this.email,
        password: this.password
      }

      try {
        await this.$store.dispatch('login', formData)
        this.$router.push("/");   
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
</style>
