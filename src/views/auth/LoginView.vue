<script setup>
import AppLayout from '@/components/layout/AppLayout.vue'
import LoginForm from '@/components/auth/LoginForm.vue'
import { useDisplay } from 'vuetify'

const { mobile } = useDisplay()
</script>

<template>
  <AppLayout>
    <template #content>
      <div class="flex items-center justify-center w-full px-4">
        <v-row class="w-full max-w-6xl items-center justify-center">
          <v-col cols="12" lg="6" v-if="!mobile" class="hidden lg:flex items-center justify-center">
            <v-img class="mx-auto drop-shadow-2xl" src="/images/login.svg" max-width="500"></v-img>
          </v-col>

          <v-col cols="12" lg="6" class="flex items-center justify-center">
            <div class="blur-card w-full max-w-md">
              <!-- Logo -->
              <div class="text-center pt-4 pb-2">
                <v-img class="mx-auto mb-3" src="/images/logo.png" :width="mobile ? '100' : '80'"></v-img>
                <h1 class="text-2xl font-bold text-white tracking-wide">WELCOME</h1>
                <p class="text-xs text-gray-200 mt-1">Sign in to continue to CSULF</p>
              </div>

              <!-- Form -->
              <div class="px-6 pb-6">
                <LoginForm></LoginForm>

                <p class="text-xs text-center text-gray-100 mt-4">
                  Don't have an account?
                  <RouterLink class="text-orange-300 hover:text-orange-200 font-semibold transition-colors" to="register">
                    Register Here
                  </RouterLink>
                </p>
              </div>
            </div>
          </v-col>
        </v-row>
      </div>
    </template>
  </AppLayout>
</template>

<style scoped>
.blur-card {
  backdrop-filter: blur(12px);
  background: rgba(30, 58, 32, 0.75);
  border-radius: 24px;
  box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
  border: 1px solid rgba(255, 255, 255, 0.18);
}
</style>
