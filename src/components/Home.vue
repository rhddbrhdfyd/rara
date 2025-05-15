<script setup>
import './css/main.css'
import Weather from './Weather.vue';
import Popup from './Popup.vue'
import { useRouter } from 'vue-router';
import { ref, onMounted } from 'vue';

const router = useRouter();
const showPopup = ref(false);
const dontShowAgain = ref(false);
const todayKey = `hidePopup_${new Date().toISOString().slice(0, 10)}`;

onMounted(() => {
  const hideToday = localStorage.getItem(todayKey);
  if (!hideToday) {
    showPopup.value = true;
  }
});

function goToEvent() {
  router.push('/event');
}

function handleClose() {
  if (dontShowAgain.value) {
    localStorage.setItem(todayKey, 'true');
  }
  showPopup.value = false;
}
</script>

<template>
  <Popup v-if="showPopup" @close="handleClose" :dont-show-again.sync="dontShowAgain" />
  <div class="title">
    <div class="wrap">
      <div class="title-img">
        <img src="https://placehold.co/1920x900?text=title" alt="홈 타이틀 이미지" />
      </div>
    </div>
  </div>
  <Weather />
</template>
<style scoped>
.event-click {
  background: white;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>