<template>
  <the-header></the-header>
  <router-view v-slot="{ Component }">
    <transition name="route" mode="out-in">
      <component :is="Component" />
    </transition>
  </router-view>
</template>

<script>
import TheHeader from '@/components/layout/TheHeader';
import { mapGetters } from 'vuex';

export default {
  name: 'App',
  components: { TheHeader },
  computed: {
    ...mapGetters(['didAutoLogout']),
  },
  methods: {
    async loadMentors(forceRefresh = false) {
      this.isLoading = true;
      try {
        await this.$store.dispatch('mentors/loadMentors', {
          forceRefresh,
        });
      } catch (err) {
        this.error = err.message || 'Something went wrong';
      }
      this.isLoading = false;
    },
  },
  watch: {
    didAutoLogout(currentVal, oldVal) {
      if (currentVal && currentVal !== oldVal) {
        this.$router.replace({ name: 'mentors' });
      }
    },
  },
  created() {
    this.$store.dispatch('tryLogin');
    this.loadMentors();
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

* {
  box-sizing: border-box;
}

html {
  font-family: 'Roboto', sans-serif;
}

body {
  margin: 0;
}

.route-enter-from {
  opacity: 0;
  transform: translateY(-30px);
}

.route-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.route-enter-active {
  transition: all 0.25s ease-out;
}

.route-leave-active {
  transition: all 0.25s ease-in;
}

.route-enter-to,
.route-leave-from {
  opacity: 1;
  transform: translateY(0);
}
.text-center {
  text-align: center;
}
</style>
