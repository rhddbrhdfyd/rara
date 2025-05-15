<template>
  <div v-if="isVisible" class="scratch-container">
    <button class="close-btn" @click="handleClose">
      <span class="icon">×</span>
    </button>
    <h2>당신을 위한 오늘의 기분 좋은 쇼핑 예보.</h2>
    <p>Your Daily Forecast for Feel-Good Finds.</p>
    <a href="#" class="scratch-box more-btn" @click.prevent="handleMore">
      더보기
    </a>
    <label class="dont-show">
      <input type="checkbox" v-model="dontShow" /> 오늘 하루 이 창 안보기
    </label>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const isVisible = ref(true);
const dontShow = ref(false);

function handleClose() {
  if (dontShow.value) {
    // 오늘 날짜를 YYYY-MM-DD 형식으로 저장
    const today = new Date().toISOString().slice(0, 10);
    localStorage.setItem('scratchClosedDate', today);
  }
  isVisible.value = false;
}

function handleMore() {
  // '더보기' 클릭 시 실제 동작 (예: 라우터 이동)
  console.log('더보기 클릭!');
}

onMounted(() => {
  // 로컬스토리지에 저장된 날짜와 오늘 날짜 비교
  const savedDate = localStorage.getItem('scratchClosedDate');
  const today = new Date().toISOString().slice(0, 10);
  if (savedDate === today) {
    isVisible.value = false;
  }
});
</script>

<style scoped>
.scratch-container {
  position: absolute;
  width: 400px;
  height: auto;
  background: #FFF9CE;
  display: flex;
  flex-direction: column;
  border-radius: 15px;
  right: 3%;
  z-index: 2;
  bottom: 7%;
  padding: 20px 65px 20px 65px;
}

.scratch-container h2 {
  font-size: 15px;
  font-weight: 800;
  color: #464e55;
  margin: 0;
}

.scratch-container p {
  font-size: 13px;
  font-weight: 800;
  color: #5d6871;
  margin: 8px 0 16px;
}

.scratch-box.more-btn {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  width: 300px;
  height: 87px;
  background-color: #b6d4ec;
  border-radius: 7px;
  font-size: 14px;
  font-weight: 700;
  color: #ffffff;
  cursor: pointer;
  text-decoration: none;
  margin-bottom: 12px;
}

.dont-show {
  display: flex;
  align-items: center;
  font-size: 12px;
  color: #464e55;
  user-select: none;
}

.dont-show input {
  margin-right: 6px;
}

.close-btn {
  width: 27px;
  height: 27px;
  padding: 0;
  background: #fff;
  border: none;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  position: absolute;
  top: 10px;
  right: 10px;
}

.close-btn .icon {
  color: #55565b;
  font-weight: 700;
  font-size: 26px;
}
</style>
