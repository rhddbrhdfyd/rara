<script setup>
import { useRoute } from 'vue-router';
import { computed } from 'vue';
import './css/sidebar.css'
import eventLogo from '../../public/img/logo-event.svg';
import defaultLogo from '../../public/img/wlogo.png';      


const route = useRoute();

const logoSrc = computed(() => {
  return route.path.startsWith('/event') ? eventLogo : defaultLogo;
});

</script>

<template>
  <div :class="['side', {'event' : route.path.startsWith('/event')}]">
    <div class="flex">
      <router-link to="/">
        <img :src="logoSrc" alt="로고" class="logo" />
      </router-link>
    </div>
  </div>
</template>
