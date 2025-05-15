<script setup>
import { useRoute } from 'vue-router';
import { weatherMap } from './js/weatherDataMap.js';
import './css/codyList.css';
import './css/codyDetail.css';
import './css/itemList.css';

import { Splide, SplideSlide } from '@splidejs/vue-splide'
import '@splidejs/vue-splide/css'
import { ref, onMounted, nextTick, computed } from 'vue'

const route = useRoute();
const weatherType = route.params.weatherType || 'rainy';
const weatherData = weatherMap[weatherType];

const codys = weatherData.cody;
const items = weatherData.items;
const select = weatherData.select?.[0]?.image
const detailImages = weatherData.detail || [];

const itemList = ref([])
const swiperRef = ref()
const showSwiper = ref(false)

onMounted(() => {
  itemList.value = weatherMap[weatherType]?.items || []

  requestAnimationFrame(() => {
    requestAnimationFrame(() => {
      showSwiper.value = true
    })
  })
})
const groupedItems = computed(() => {
  const groups = []
  for (let i = 0; i < itemList.value.length; i += 2) {
    groups.push(itemList.value.slice(i, i + 2))
  }
  return groups
})

const weatherTypeLabel = {
  rainy: '비 오는 날',
  sunny: '화창한 날',
  snowy: '눈 오는 날',
  cloudy: '흐린 날',
}[weatherType];
</script>

<template>
  <section id="codylist">
    <div class="wrap">
      <div class="cody-layout">
        <div class="cody-left">
          <div class="cody-list">
            <div
              v-for="select in weatherData.cody"
              :key="select.id"
              class="s-card"
            >
              <img :src="select.image" alt="코디 이미지" class="s-image" />
              <p class="cody-desc">{{ select.desc }}</p>
            </div>
          </div>

        </div>

        <div class="cody-right">
          <h3 class="a-title">{{ weatherTypeLabel.value }} 이렇게 해보세요</h3>
          <ul class="a-lists">
            <li v-for="(a, i) in weatherData.activities" :key="i" class="a-list">
              {{ a.title }}
            </li>
          </ul>
        </div>
      </div>
    </div>
  </section>
  <section id="detail">
    <div class="wrap">
      <div class="d-row">
        <div
          class="group-card"
          v-for="cody in codys"
          :key="cody.id"
        >
          <img  :src="detailImages.find(d => d.group === cody.group)?.image"  alt="디테일 이미지"  class="s-image"/>

          <!-- 하단 아이템 리스트 -->
          <div class="d-card">
            <ul class="d-lists">
              <li
                v-for="item in items.filter(i => i.group === cody.group)"
                :key="item.id"
                class="d-list"
              >
                <img :src="item.image" alt="아이템 이미지" class="d-thumb" />
                <div class="d-text">
                  <strong>{{ item.brand }}</strong>
                  <p>{{ item.desc }}</p>
                  <span>
                    <span class="color-dot" :style="{ backgroundColor: item.colorCode }"></span>
                    <p>{{ item.color }}</p>
                  </span>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <div class="side">

    </div>
  </section>
  <section id="item">
    <div class="wrap flex">
      <div class="i-layout">
        <div class="i-main">
          <img :src="weatherData.select[0].image" alt="코디 대표 이미지" class="i-image" />
        </div>

      <div class="i-box" v-if="showSwiper">
        <Splide
          :options="{
            perPage: 2,
            type: 'slide', // loop이 필요 없으면 slide로도 가능
            gap: '20px',
            arrows: false, // 좌우 화살표 보여줄지 여부
            pagination: false, // 아래 dot 표시
            drag: true,
            autoplay: false,  // 자동 넘김 비활성화
          }"
        >
          <SplideSlide v-for="(group, i) in groupedItems" :key="i">
            <div class="vertical-group">
              <div v-for="item in group" :key="item.id" class="i-card">
                <img :src="item.image" class="i-thumb" />
                <div class="i-info">
                  <strong>{{ item.brand }}</strong>
                  <p>{{ item.desc }}</p>
                  <p>{{ item.price }}</p>
                  <span>
                    <button>구매하기</button>
                    <button>장바구니</button>
                  </span>
                </div>
              </div>
            </div>
          </SplideSlide>
        </Splide>
      </div>

      </div>
    </div>
  </section>
</template>
