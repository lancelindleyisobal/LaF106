<script setup>
import { ref } from 'vue'
import { supabase, formActionDefault } from '../../utils/supabase.js'
import { requiredValidator, emailValidator } from '@/utils/validators'
import AlertNotification from '../common/AlertNotification.vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/authUser' // Correct import

const visible = ref(false)
const refVForm = ref()

//predefined vue functions
const router = useRouter()

//load variables
const FormDataDefault = {
  email: '',
  password: ''
}

const formData = ref({
  ...FormDataDefault
})

const formAction = ref({
  ...formActionDefault
})

const onSubmit = async () => {
  const authStore = useAuthStore()
  formAction.value = { ...formActionDefault } ///reset error message
  formAction.value.formProcess = true

  const { data, error } = await supabase.auth.signInWithPassword({
    email: formData.value.email,
    password: formData.value.password
  })

  if (error) {
    console.error(error)
    formAction.value.formErrorMessage = error.message
    formAction.value.formStatus = error.status
  } else if (data) {
    console.log(data)
    formAction.value.formSuccessMessage = 'Successfully Logged Account'

    // Update the auth store
    authStore.login(data.user, data.session.access_token)

    setTimeout(() => {
      router.replace('/system/dashboard')
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

////google login
const onGoogleSignIn = async () => {
  try {
    // Step 1: Google Login (OAuth)
    const { error } = await supabase.auth.signInWithOAuth({
      provider: 'google',
      options: {
        redirectTo: `${window.location.origin}/system/dashboard` // Redirect URI
      }
    })

    if (error) {
      console.error('Google Login Error:', error.message)
      return
    }

    // Step 2: Wait for session data
    const sessionData = await sessionWait()

    if (!sessionData?.session?.user) {
      console.error('Session not received from Google login.')
      return
    }

    const user = sessionData.session.user
    const {
      given_name: firstname,
      family_name: lastname,
      picture,
      full_name,
      email
    } = user.user_metadata || {}

    // Ensure required user data is available
    if (!email) {
      console.error('Email is required but not available.')
      return
    }

    // Step 3: Prepare data for insertion into the database
    const updateData = {
      user_id: user.id,
      firstname: full_name || 'Unknown',
      lastname: lastname || 'Unknown',
      profile_pic: picture || 'default-profile-pic.jpg',
      verifiedEmail: !!user.email_confirmed_at,
      token: user.id // This might not be a token, consider renaming
    }

    // Step 4: Check if user already exists
    const { data: existingUser, error: existingUserError } = await supabase
      .from('auth.users') // Adjust the schema and table name here if needed
      .select('id') // Only select the ID to minimize payload
      .eq('email', email)
      .single() // Expect a single result

    if (existingUserError && existingUserError.code !== 'PGRST116') {
      // PGRST116 = no rows returned
      console.error('Error checking existing user:', existingUserError.message)
      return
    }

    // Step 5: Insert or Update user data
    if (existingUser && existingUser.length > 0) {
      // Step 5: Update the existing user's `raw_user_meta_data`
      const { error: updateError } = await supabase
        .from('auth.users') // Target the correct schema and table
        .update({
          raw_user_meta_data: {
            firstname: full_name || 'Unknown',
            lastname: lastname || 'Unknown',
            profile_pic: picture || 'default-profile-pic.jpg',
            verifiedEmail: user.email_confirmed_at ? true : false,
            token: user.id
          }
        })
        .eq('email', email) // Update based on the user's email

      if (updateError) {
        console.error('Error updating user:', updateError.message)
        return
      }
    } else {
      // Step 6: Insert new user with `raw_user_meta_data`
      const { error: insertError } = await supabase
        .from('auth.users') // Target the correct schema and table
        .insert([
          {
            email, // Ensure required fields like `email` are included
            raw_user_meta_data: {
              firstname: full_name || 'Unknown',
              lastname: lastname || 'Unknown',
              profile_pic: picture || 'default-profile-pic.jpg'
            }
          }
        ])

      if (insertError) {
        console.error('Error inserting new user:', insertError.message)
        return
      }
    }

    console.log('User data successfully inserted or updated.')
  } catch (err) {
    console.error('Unexpected Error:', err)
  }
}



</script>

<template>
  <AlertNotification
    :form-success-message="formAction.formSuccessMessage"
    :form-error-message="formAction.formErrorMessage"
  ></AlertNotification>

  <v-form class="space-y-3 mt-3" ref="refVForm" fast-fail @submit.prevent="onFormSubmit">
    <!-- Email Field -->
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

    <!-- Password Field -->
    <div>
      <label class="block text-xs font-medium text-gray-200 mb-1">Password</label>
      <v-text-field
        color="green-darken-2"
        bg-color="rgba(255, 255, 255, 0.9)"
        v-model="formData.password"
        rounded="lg"
        :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
        :type="visible ? 'text' : 'password'"
        placeholder="Enter your password"
        variant="solo"
        density="compact"
        @click:append-inner="visible = !visible"
        :rules="[requiredValidator]"
        hide-details="auto"
        class="modern-input"
      ></v-text-field>
    </div>

    <!-- Sign In Button -->
    <v-btn
      rounded="lg"
      class="mt-4 text-sm font-semibold shadow-lg hover:shadow-xl transition-all"
      size="default"
      type="submit"
      block
      color="#e65100"
      :loading="formAction.formProcess"
      elevation="4"
    >
      Sign In
    </v-btn>

    <!-- Divider -->
    <div class="relative my-4">
      <div class="absolute inset-0 flex items-center">
        <div class="w-full border-t-2" style="border-color: #e5e7eb !important;"></div>
      </div>
      <div class="relative flex justify-center text-xs">
        <span class="px-3 bg-[rgba(30,58,32)] text-gray-200 font-medium">or continue with</span>
      </div>
    </div>

    <!-- Google Sign In Button -->
    <v-btn
      rounded="lg"
      class="text-xs font-medium border-2 shadow-md hover:shadow-lg transition-all"
      size="default"
      block
      color="white"
      variant="flat"
      @click="onGoogleSignIn"
    >
      <img
        src="/images/google.png"
        alt="Google logo"
        class="w-4 h-4 mr-2"
      />
      <span class="text-gray-700">Continue with Google</span>
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
