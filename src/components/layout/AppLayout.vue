<template>
  <v-responsive>
    <v-app>
      <!-- Background image container - fixed height, no scroll -->
      <div class="h-screen w-screen bg-cover bg-center overflow-y-auto flex flex-col" 
           style="background-image: url('/images/background.svg'); background-color: #144517;">
        <v-main class="w-full flex-1 flex items-center justify-center" transition="fade-transition">
          <v-container class="py-8">
            <slot name="content"></slot>
          </v-container>
        </v-main>
        
        <!-- Footer -->
        <footer class="footer-container backdrop-blur-sm">
          <div class="max-w-6xl mx-auto px-6">
            <div class="text-center space-y-2">
              <div class="text-sm font-semibold text-white tracking-wide">
                Caraga State University - Lost and Found System
              </div>
              <div class="flex items-center justify-center gap-3">
                <div class="w-12 h-px bg-white/40"></div>
                <span class="text-xs text-white/80">© 2025 All Rights Reserved</span>
                <div class="w-12 h-px bg-white/40"></div>
              </div>
            </div>
          </div>
        </footer>
      </div>
    </v-app>
  </v-responsive>
</template>

<style scoped>
.footer-container {
  background: linear-gradient(135deg, rgba(230, 81, 0, 0.95) 0%, rgba(191, 54, 12, 0.95) 100%);
  padding: 20px 0;
  box-shadow: 0 -4px 12px rgba(0, 0, 0, 0.15);
}
</style>
