<script setup>
import {
  requiredValidator,
  emailValidator,
  passwordValidator,
  confirmedValidator
} from '@/utils/validators'
import { ref } from 'vue'
import AlertNotification from '../common/AlertNotification.vue'
import { supabase, formActionDefault } from '../../utils/supabase.js'
import { useRouter } from 'vue-router'

const visible = ref(false)
const isVisible = ref(false)
const refVForm = ref()

//predefined vue functions
const router = useRouter()

const FormDataDefault = {
  firstname: '',
  lastname: '',
  facebook_link: '',
  email: '',
  password: '',
  passwordConfirmation: '',
  profile_pic: 'https://ndmbunubneumkuadlylz.supabase.co/storage/v1/object/public/profile/public/profile-default.png' // Default profile picture
}

const formData = ref({
  ...FormDataDefault
})

const formAction = ref({
  ...formActionDefault
})

const onSubmit = async () => {
  formAction.value = { ...formActionDefault } ///reset error message
  formAction.value.formProcess = true

  const { data, error } = await supabase.auth.signUp({
    email: formData.value.email,
    password: formData.value.password,
    options: {
      data: {
        firstname: formData.value.firstname,     //customs object
        lastname: formData.value.lastname,
        facebook_link: formData.value.facebook_link,
        profile_pic: formData.value.profile_pic
      }
    }
  })

  if (error) {
    console.error(error)
    formAction.value.formErrorMessage = error.message
    formAction.value.formStatus = error.status
  } else if (data) {
    console.log(data)
    formAction.value.formSuccessMessage = 'Successfully Registered'
    formAction.value.formSuccessMessage = 'Please Verify your Email to Login'

    // Add a 5-second delay before redirecting to the login page
    setTimeout(() => {
      router.replace('/login')
    }, 5000) // 5000 milliseconds = 5 seconds
  }

  //reset form
  refVForm.value?.reset() //clear the field if successfull login
  formAction.value.formProcess = false
}

const onFormSubmit = () => {
  refVForm.value?.validate().then(({ valid }) => {
    if (valid) onSubmit()
  })
}
</script>

<template>
  <AlertNotification
    :form-success-message="formAction.formSuccessMessage"
    :form-error-message="formAction.formErrorMessage"
  ></AlertNotification>

  <v-form class="space-y-2 mt-3" ref="refVForm" fast-fail @submit.prevent="onFormSubmit">
    <!-- Name Fields Row -->
    <div class="grid grid-cols-2 gap-2">
      <div>
        <label class="block text-xs font-medium text-gray-200 mb-1">First Name</label>
        <v-text-field
          color="green-darken-2"
          bg-color="rgba(255, 255, 255, 0.9)"
          rounded="lg"
          v-model="formData.firstname"
          placeholder="First name"
          variant="solo"
          density="compact"
          :rules="[requiredValidator]"
          hide-details="auto"
          class="modern-input"
        ></v-text-field>
      </div>
      <div>
        <label class="block text-xs font-medium text-gray-200 mb-1">Last Name</label>
        <v-text-field
          color="green-darken-2"
          bg-color="rgba(255, 255, 255, 0.9)"
          rounded="lg"
          v-model="formData.lastname"
          placeholder="Last name"
          variant="solo"
          density="compact"
          :rules="[requiredValidator]"
          hide-details="auto"
          class="modern-input"
        ></v-text-field>
      </div>
    </div>

    <!-- Facebook Link -->
    <div>
      <label class="block text-xs font-medium text-gray-200 mb-1">Facebook Profile</label>
      <v-text-field
        color="green-darken-2"
        bg-color="rgba(255, 255, 255, 0.9)"
        rounded="lg"
        v-model="formData.facebook_link"
        placeholder="Facebook profile link"
        variant="solo"
        density="compact"
        :rules="[requiredValidator]"
        hide-details="auto"
        class="modern-input"
      ></v-text-field>
    </div>

    <!-- Email -->
    <div>
      <label class="block text-xs font-medium text-gray-200 mb-1">Email Address</label>
      <v-text-field
        color="green-darken-2"
        bg-color="rgba(255, 255, 255, 0.9)"
        rounded="lg"
        v-model="formData.email"
        placeholder="Enter your email"
        :rules="[requiredValidator, emailValidator]"
        variant="solo"
        density="compact"
        hide-details="auto"
        class="modern-input"
      ></v-text-field>
    </div>

    <!-- Password -->
    <div>
      <label class="block text-xs font-medium text-gray-200 mb-1">Password</label>
      <v-text-field
        color="green-darken-2"
        bg-color="rgba(255, 255, 255, 0.9)"
        rounded="lg"
        v-model="formData.password"
        :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
        :type="visible ? 'text' : 'password'"
        placeholder="Create password"
        variant="solo"
        density="compact"
        @click:append-inner="visible = !visible"
        :rules="[requiredValidator, passwordValidator]"
        hide-details="auto"
        class="modern-input"
      ></v-text-field>
    </div>

    <!-- Confirm Password -->
    <div>
      <label class="block text-xs font-medium text-gray-200 mb-1">Confirm Password</label>
      <v-text-field
        color="green-darken-2"
        bg-color="rgba(255, 255, 255, 0.9)"
        rounded="lg"
        v-model="formData.passwordConfirmation"
        :append-inner-icon="isVisible ? 'mdi-eye-off' : 'mdi-eye'"
        :type="isVisible ? 'text' : 'password'"
        placeholder="Confirm password"
        variant="solo"
        density="compact"
        @click:append-inner="isVisible = !isVisible"
        :rules="[
          requiredValidator,
          confirmedValidator(formData.passwordConfirmation, formData.password)
        ]"
        hide-details="auto"
        class="modern-input"
      ></v-text-field>
    </div>

    <!-- Sign Up Button -->
    <v-btn
      rounded="lg"
      class="mt-4 text-sm font-semibold shadow-lg hover:shadow-xl transition-all"
      size="default"
      type="submit"
      block
      color="#e65100"
      :disabled="formAction.formProcess"
      :loading="formAction.formProcess"
      elevation="4"
    >
      Create Account
    </v-btn>
  </v-form>
</template>

<style scoped>
.modern-input :deep(.v-field) {
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
  transition: all 0.2s;
}

.modern-input :deep(.v-field:hover) {
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.modern-input :deep(.v-field--focused) {
  box-shadow: 0 4px 12px -1px rgba(76, 175, 80, 0.3);
}

/* Bright red error messages */
.modern-input :deep(.v-messages__message) {
  color: #ff1744 !important;
  font-weight: 600;
  font-size: 0.75rem;
}

.modern-input :deep(.v-field--error) {
  box-shadow: 0 0 0 2px rgba(255, 57, 96, 0.8);
}
</style>
