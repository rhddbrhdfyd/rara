<template>
  <div v-if="isVisible" class="scratch-wrapper">
    <div class="scratch-border">
      <!-- 초록색 중앙 박스 -->
      <div class="scratch-box">
        <div class="box-top">
          <img src="../assets/scracth1.svg" alt="로고">
          <div class="title-hint">
            <h2> 오늘의 기분 예보를 긁어보세요!</h2>
            <p>"기분 맑음, 행운 확률 100% 행운의 스크래치 복권!"</p>
           </div>
        </div>
        <!-- 긁어보세요 힌트 -->
        <div v-if="isHintVisible && !isCleared" class="scratch-hint">긁어보세요</div>

        <!-- 긁는 캔버스 -->
        <div class="scratch-area">
          <canvas
            ref="canvas"
            :class="{ cleared: isCleared, 'fade-out': isClearing }"
            @mousedown="handleStart"
            @mousemove="handleMove"
            @mouseup="handleEnd"
            @touchstart="handleStart"
            @touchmove="handleMove"
            @touchend="handleEnd"
          ></canvas>

          <!-- 긁으면 나타나는 당첨 메시지 -->
          <div class="content">
            <h3>{{ selectedMessage.title }}</h3>
            <p>{{ selectedMessage.desc }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const messages = [
  { title: "원피스 할인 쿠폰 2000원 할인", desc: "햇살 가득한 하루, 가벼운 원피스 하나 어때요?" },
  { title: "니트 할인 쿠폰 2000원 할인", desc: "흐린 날엔 부드러운 니트가 마음을 감싸줄 거예요." },
  { title: "우산 할인 쿠폰 2000원 할인", desc: "비 오는 날, 귀여운 우산과 함께 기분 전환 완료!" },
  { title: "머플러 할인 쿠폰 2000원 할인", desc: "눈 내리는 오늘, 포근한 머플러로 따뜻하게." },
  { title: "전 상품 할인 쿠폰 4000원 할인", desc: "오늘은 당신만의 색을 입는 날이에요. 밝게 빛나봐요!" },
  { title: "전 상품 할인 쿠폰 3000원 할인", desc: "좋아하는 음악과 어울리는 옷을 골라볼까요?" },
  { title: "전 상품 할인 쿠폰 2000원 할인", desc: "몽글몽글할 땐, 파스텔 컬러로 마음까지 부드럽게." },
  { title: "전 상품 할인 쿠폰 2000원 할인", desc: "잔잔한 바람처럼 여유로운 스타일을 입어보세요." },
  { title: "배송비 할인 쿠폰 2000원 할인", desc: "행운 배송 중! 당신에게 아이템이 찾아오고 있어요." },
  { title: "전 상품 할인 쿠폰 5000원 할인", desc: "오늘의 기분 예보: 설렘 70%, 기대 30%, 쇼핑 100%!" }
];

const selectedMessage = ref({ title: '', desc: '' });
const isVisible = ref(true);
const isCleared = ref(false);
const isClearing = ref(false);
const isHintVisible = ref(true);

const canvas = ref(null);
let ctx;
let isDrawing = false;
let lastPos = null;
let scratchCheckCounter = 0;

function handleClose() {
  isVisible.value = false;
}

function getPos(e) {
  const rect = canvas.value.getBoundingClientRect();
  const x = (e.touches ? e.touches[0].clientX : e.clientX) - rect.left;
  const y = (e.touches ? e.touches[0].clientY : e.clientY) - rect.top;
  return { x, y };
}

function handleStart(e) {
  isHintVisible.value = false;
  isDrawing = true;
  lastPos = getPos(e);
  draw(e);
}

function handleMove(e) {
  if (!isDrawing || isCleared.value) return;
  draw(e);
}

function handleEnd() {
  isDrawing = false;
  lastPos = null;
}


function draw(e) {
  const pos = getPos(e);
  if (!lastPos) lastPos = pos;

  ctx.lineJoin = 'round';
  ctx.lineCap = 'round';
  ctx.lineWidth = 70;
  ctx.beginPath();
  ctx.moveTo(lastPos.x, lastPos.y);
  ctx.lineTo(pos.x, pos.y);
  ctx.stroke();
  lastPos = pos;

  scratchCheckCounter++;
  if (scratchCheckCounter % 10 === 0) {
    checkScratchRatio();
  }
}

function checkScratchRatio() {
  const pixels = ctx.getImageData(0, 0, canvas.value.width, canvas.value.height);
  const total = pixels.data.length / 4;
  let cleared = 0;

  for (let i = 3; i < pixels.data.length; i += 4) {
    if (pixels.data[i] === 0) cleared++;
  }

  const ratio = cleared / total;
  if (ratio > 0.6 && !isCleared.value) {
    isClearing.value = true;
    setTimeout(() => {
      isCleared.value = true;
      canvas.value.classList.add('cleared');
    }, 800);
  }
}

onMounted(() => {
  const randomIndex = Math.floor(Math.random() * messages.length);
  selectedMessage.value = messages[randomIndex];

  const c = canvas.value;
  const dpr = window.devicePixelRatio || 1;
  c.width = 520 * dpr;
  c.height = 120 * dpr;
  c.getContext('2d').scale(dpr, dpr);
  ctx = c.getContext('2d');

  ctx.fillStyle = '#e3e3e3';
  ctx.fillRect(0, 0, 520, 120);
  ctx.globalCompositeOperation = 'destination-out';

  c.addEventListener('touchmove', e => e.preventDefault(), { passive: false });
});
</script>

<style scoped>
.scratch-wrapper {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

/* 체크 테두리 감싸는 div */
.scratch-border {
  padding: 20px;
  background-image: repeating-conic-gradient(#fff 0% 25%, #ffbdca 0% 50%);
  background-size: 20px 20px;
  display: inline-block;
}

/* 초록색 박스 */
.scratch-box {
  background-color: #FFD767FC;
  width: 620px;
  height: 270px;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.box-top {
  display: grid;
  grid-template-columns: 100px auto;
  grid-template-rows: auto;
  width: 500px;
  height: 100px;
}

.box-top img {
  width: 100px;
  height: 100px;
}

.title-hint {
  display: flex;
  flex-direction: column;
  align-items: center;
}
/* 문구 */
.title-hint h2 {
  background: #FF5B79;
  border-radius: 30px;
  font-weight: bold;
  font-size: 22px;
  color: #ffffff;
  margin-top: 13px;
  height: 43px;
  width: 330px;
  text-align: center;
  padding-top: 4px;
  text-shadow: 2px 3px 3px rgba(0, 0, 0, 0.31);
}
.title-hint p {
font-weight: 700;
color: #222222;
margin-top: 8px;
font-size: 17px;
}
/* 새로운 영역: 캔버스와 컨텐츠 겹치게 */
.scratch-area {
  position: relative;
  width: 520px;
  height: 120px;
}

/* 긁기 캔버스 */
canvas {
  width: 520px;
  height: 100px;
  position: absolute;
  bottom: 0;
  left: 0;
  z-index: 2;
  cursor: pointer;
}

/* 긁기 완료 후 사라짐 */
canvas.fade-out {
  opacity: 0;
  transition: opacity 0.8s ease;
}
canvas.cleared {
  display: none;
}

/* 긁은 후 나타나는 내용 */
.content {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 520px;
  height: 100px;
  background: #fff;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: #1E3A5F;
  font-weight: bold;
  z-index: 1; /* canvas 아래 */
  pointer-events: none; /* 긁을 수 있게 */
}

.content h3 {
  order: 2;
  font-size: 17px;
  margin-top: 10px;
  color: #4e5052;
  font-weight: 800;
}

.content p {
  order: 1;
  font-size: 13px;
  margin-top: 0;
  color: #6a6e72;
  font-weight: 800;
}

/* 힌트 애니메이션 */
@keyframes pulseText {
  0%, 100% {
    color: #3a3a3a;
  }
  50% {
    color: #5c5c5c;
  }
}
@keyframes growShrink {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

.scratch-hint {
  position: absolute;
  bottom: 68px;;
  z-index: 3;
  font-size: 14px;
  font-weight: 700;
  color: #3a3a3a;
  animation: pulseText 1.5s infinite ease-in-out, growShrink 1.5s infinite ease-in-out;
}

</style>
