<script setup>
import { ref, onMounted } from 'vue'
import { supabase } from '@/utils/supabase'

const userCount = ref(0)

const fetchUserCount = async () => {
  const { data, error } = await supabase.rpc('count_users') // Use the rpc method to call the function

  if (error) {
    console.error('Error fetching user count:', error)
  } else {
    userCount.value = data // Set the result to the userCount ref
  }
}

onMounted(fetchUserCount)
</script>

<template>
  <v-app>
    <br />
    <v-row justify="center">
      <v-col cols="12" sm="12" md="8">
        <br /><br />
        <br />
        <v-card class="rounded-xl mb-4" max-width="1000" elevation="4">
          <v-list class="text-center pt-5">
            <div>
              <v-avatar size="200" class="mx-auto">
                <v-img src="/images/csulf.png" />
              </v-avatar>
              <p class="text-light-green-darken-3 font-weight-bold text-h5">About the System</p>
            </div>
            <div>
              <p class="text-light-green-darken-3 ma-6">
                This application is designed exclusively for the students of Caraga State
                University, providing a seamless platform for managing lost and found items within
                the university premises. Whether you’ve misplaced something or stumbled upon an item
                that someone else might have lost, this system serves as a bridge to connect the
                community. By enabling users to post detailed information about lost or found
                belongings, the application ensures transparency and ease of communication,
                fostering a supportive and connected environment for everyone on campus.
              </p>
            </div>
          </v-list>
        </v-card>

        <br />

        <v-card class="rounded-xl mb-4" max-width="1000" elevation="4">
          <v-list class="text-center pa-5">
            <!-- Section Title -->
            <div>
              <p class="text-light-green-darken-3 font-weight-bold text-h5">About the Developer</p>
            </div>

            <!-- Description -->
            <div>
              <p class="text-light-green-darken-3 ma-6">
                This innovative solution is the collaborative effort of a dedicated group of Caraga
                State University students. The development team includes
                <b
                  >Cres Steven P. Buque, who served as the System Analyst, Frontend Developer,
                   and the Backend Developer.</b
                >
                Combined expertise and teamwork have brought this vision to life, aiming to
                improve the university experience by addressing a common issue with a practical and
                user-friendly application.
              </p>
            </div>

            <!-- Developer Photos -->
            <v-row justify="center" class="mt-4 pa-3">
              

              <!-- Developer 2 -->
              <v-col cols="12" sm="4" md="4" lg="3" class="text-center">
                <v-avatar size="100" class="mx-auto" color="black">
                  <v-img src="/images/Buque.jpg" alt="Cres Steven P. Buque" class="rounded" />
                </v-avatar>
                <p class="text-light-green-darken-3 font-weight-bold mt-2">Cres Steven P. Buque</p>
                <p class="text-light-green-darken-3 text-caption"> System Analyst & Frontend Developer</p>
              </v-col>

            </v-row>
          </v-list>
        </v-card>

        <br />

        <v-card class="rounded-xl mb-4" max-width="1000" elevation="4">
          <v-list class="text-center pa-5">
            <!-- Developers info -->
            <div class="d-flex flex-column align-center">
              <v-img :width="120" aspect-ratio="1/1" cover src="/images/csulogo.png" />
              <p class="text-light-green-darken-3 font-weight-bold text-h7">
                © Caraga State University - Lost and Found
              </p>
            </div>
            <div>
              <p class="text-light-green-darken-3 font-weight-bold text-h7">
                All Rights Reserved 2026
              </p>
            </div>
          </v-list>
        </v-card>
      </v-col>
    </v-row>
  </v-app>
</template>
