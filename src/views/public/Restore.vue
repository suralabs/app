<template>
  <div class="flex items-center min-h-screen p-6 bg-gray-50 dark:bg-gray-900">
    <div class="flex-1 h-full max-w-4xl mx-auto overflow-hidden bg-white rounded-lg shadow-xl dark:bg-gray-800">
      <div class="flex flex-col overflow-y-auto md:flex-row">
        <div class="h-32 md:h-auto md:w-1/2">
          <img aria-hidden="true" class="object-cover w-full h-full dark:hidden"
               :src="imgForgot" alt="Office">
          <img aria-hidden="true" class="hidden object-cover w-full h-full dark:block"
               :src="imgForgotDark" alt="Office">
        </div>
        <main class="flex items-center justify-center p-6 sm:p-12 md:w-1/2">
          <form v-if="restore_status == false && $route.query.hash == null" class="w-full" @submit.prevent="restore">
            <h1 class="mb-4 text-xl font-semibold text-gray-700 dark:text-gray-200">Forgot password</h1>
            <label class="block text-sm text-gray-700 dark:text-gray-400">
              <span>Email</span>
              <input class="block w-full text-sm focus:outline-none dark:text-gray-300 form-input leading-5 focus:border-purple-400 dark:border-gray-600 focus:shadow-outline-purple dark:focus:border-gray-600 dark:focus:shadow-outline-gray dark:bg-gray-700 mt-1"
                  type="text" placeholder="example@example.ru" v-model="user.email" >
            </label>
            <button type="submit"
              class="align-bottom inline-flex items-center justify-center cursor-pointer leading-5 transition-colors duration-150 font-medium focus:outline-none px-4 py-2 rounded-lg text-sm text-white bg-purple-600 border border-transparent active:bg-purple-600 hover:bg-purple-700 focus:shadow-outline-purple w-full mt-4">
              {{ $t('auth.recover_password') }}
            </button>               
          </form>
          <div v-if="restore_status == true && $route.query.hash == null" class="w-full">
            <div class="mb-4 text-xl font-semibold text-gray-700 dark:text-gray-200">
              На ваш электронный ящик
              были высланы инструкции по восстановлению пароля.</div>
          </div>
          <form v-if="$route.query.hash != null" class="w-full" @submit.prevent="change_pass">
            <h1 class="mb-4 text-xl font-semibold text-gray-700 dark:text-gray-200">Forgot password</h1>

            <label class="block text-sm text-gray-700 dark:text-gray-400">
              <span>{{ $t('auth.password') }}</span>
              <input class="block w-full text-sm focus:outline-none dark:text-gray-300 form-input leading-5 focus:border-purple-400 dark:border-gray-600 focus:shadow-outline-purple dark:focus:border-gray-600 dark:focus:shadow-outline-gray dark:bg-gray-700 mt-1"
                  type="password" placeholder="********" v-model="user.password" >
            </label>
            <label class="block text-sm text-gray-700 dark:text-gray-400">
              <span>{{ $t('auth.password_confirm') }}</span>
              <input class="block w-full text-sm focus:outline-none dark:text-gray-300 form-input leading-5 focus:border-purple-400 dark:border-gray-600 focus:shadow-outline-purple dark:focus:border-gray-600 dark:focus:shadow-outline-gray dark:bg-gray-700 mt-1"
                  type="password" placeholder="********" v-model="user.re_password" >
            </label>            
            <div v-if="form_alert">
              {{ err_info }}
            </div>
            <button type="submit"
              class="align-bottom inline-flex items-center justify-center cursor-pointer leading-5 transition-colors duration-150 font-medium focus:outline-none px-4 py-2 rounded-lg text-sm text-white bg-purple-600 border border-transparent active:bg-purple-600 hover:bg-purple-700 focus:shadow-outline-purple w-full mt-4">
              {{ $t('auth.save_password') }}
            </button>  
          </form>
        </main>
      </div>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
import {useI18n} from 'vue-i18n'

import imgForgot from "@/assets/images/forgot-password.jpeg";
import imgForgotDark from "@/assets/images/forgot-password-dark.jpeg";

export default {
  name: 'RestorePage',
  components: {
  },
  props: ['user'],
  setup() {
    // use global scope
    const {t, locale} = useI18n()
    return {t, locale}
  },
  data() {
    return {
      restore_status: false,
      imgForgot:imgForgot,
      imgForgotDark:imgForgotDark
    }
  },
  mounted() {
    this.restore_status = false
  },
  methods: {
    restore() {
      Axios.post(import.meta.env.VITE_DOMAIN_API + "account/restore", this.user)
        .then(res => {
          if (res.data.status == 4) {
            // this.form_alert = true;
            // this.err_info = this.t('auth.the_mail_was_entered_incorrectly');
          }
          if (res.data.status == 5) {
            // this.form_alert = true;
            // this.err_info = this.t('auth.the_mail_was_entered_incorrectly');
          }
          if (res.data.status == 20) {
            // this.form_alert = true;
            // this.err_info = this.t('err.unknown_error');
          }

          if (res.data.status == 1) {
            // console.log(res.data.data.access_token);
            // localStorage.setItem('token', res.data.data.access_token);
            // this.$router.push('/')
            this.restore_status = true

          }
        })
    },
    change_pass(){
      this.user.hash = this.$route.query.hash
      Axios.post(import.meta.env.VITE_DOMAIN_API + "account/reset_password", this.user)
        .then(res => {
          if (res.data.status == 9) {
            this.$router.push('/restore')
          }
          if (res.data.status == 5) {
            this.form_alert = true;
            this.err_info = this.t('auth.the_pass_was_entered_incorrectly');
          }
          if (res.data.status == 1) {
            // console.log(res.data.data.access_token);
            localStorage.setItem('token', res.data.access_token);
            this.$router.push('/')
            // this.restore_status = true
          }
        })
    }
  },
}
</script>