<script setup>
import { useRouter } from 'vue-router';
import { Calendar } from 'v-calendar';
import Sidebar from './Sidebar.vue'

const router = useRouter();

const tiles = [
  { 
    type: 'sunny',
    image: '/assets/sunny.svg', 
    title: '맑음',
    description: '햇살 좋은 날이에요 ☀️',
    clickable: true
    },
  { type: 'cloudy', image: 'https://placehold.co/340x340?text=cloudy', clickable: true },
  { type: 'rainy', image: 'https://placehold.co/340x340?text=rainy', clickable: true },
  { type: 'snowy', image: 'https://placehold.co/340x340?text=snowy', clickable: true },
  { type: 'empty', image: 'https://placehold.co/340x340?text=empty', clickable: true },
  { type: 'event', image: 'https://placehold.co/340x340?text=event', clickable: true },
];

function goToWeather(type) {
  if (type === 'event') {
    router.push('/event');
  } else {
    router.push(`/${type}`);
  }
}
import { ref } from 'vue';

const calendarMarks = ref([
  {
    dates: new Date(),
    dot: true,
    popover: {
      label: '오늘'
    }
  }
]);
</script>

<template>
  <Sidebar />
  <section id="weather">
    <div class="wrap">
      <HeaderNav />
      <div class="w-grid">
         <div class="tile-text">
          <h2>오늘의 날씨를<br>
          골라보세요!</h2>
          <p>날씨에 맞는 옷과 활동을<br>
          추천해드려요</p>
        </div>
        <div
          v-for="tile in tiles"
          :key="tile.type"
          class="tile"
          @click="goToWeather(tile.type)"
        >
          <img :src="tile.image" :alt="tile.title" class="tile-img" />
          <div class="tile-content">
            <h2 class="tile-title">{{ tile.title }}</h2>
            <p class="tile-desc">{{ tile.description }}</p>
          </div>
        </div>
        <div class="calendar">
        <Calendar
    mode="month"
    is-expanded
    :attributes="calendarMarks"
     style="transform: scale(1.26) translate(-25px, -30px); transform-origin: top left;"
      />
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.calendar {
  max-width: 350px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

</style>