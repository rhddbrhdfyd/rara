<template>
  <div v-if="isVisible" class="scratch-wrapper">
    <div class="scratch-border">
      <!-- 초록색 중앙 박스 -->
      <div class="scratch-box">
        <div class="box-top">
          <img src="../assets/scratch1.svg" alt="복권이미지지">
          <div class="title-hint">
            <h2> 오늘의 기분 예보를 긁어보세요!</h2>
            <p>"기분 맑음, 행운 확률 100% 행운의 스크래치 복권!"</p>
           </div>
        </div>

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
         <p class="explanation">본 이벤트는 ID당 1회 참여 가능합니다.</p>
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

  // ✅ 1. 물리적 해상도 (픽셀 단위)
  c.width = 550 * dpr;
  c.height = 100 * dpr;

  // ✅ 2. CSS 해상도는 고정
  c.style.width = '550px';
  c.style.height = '100px';

  ctx = c.getContext('2d');
  ctx.scale(dpr, dpr); // ✅ 반드시 ctx 초기화 후에!

  // ✅ 3. 배경 그리기
  ctx.fillStyle = '#A8A8A8';
  ctx.fillRect(0, 0, 550, 100); // 단위 주의!

  // ✅ 4. 텍스트 그리기
  ctx.save();
  ctx.translate(260, 50); // 중심 회전 좌표 (CSS 사이즈 기준)
  ctx.rotate(-0.52); // 약 -30도 회전

  ctx.font = '20px Arial';
  ctx.fillStyle = 'rgba(255,255,255,0.3)';
  const text = 'LARA 복권';
  const textWidth = ctx.measureText(text).width;
  const xGap = textWidth + 20;
  const yGap = 40;

  for (let y = -200; y < 200; y += yGap) {
    for (let x = -400; x < 400; x += xGap) {
      ctx.fillText(text, x, y);
    }
  }

  ctx.restore();

  // ✅ 5. 긁기 처리
  ctx.globalCompositeOperation = 'destination-out';

  // ✅ 6. 모바일 터치 스크롤 방지
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

.scratch-box {
  background-color: #FFD767FC;
  width: 680px;
  height: 280px;
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
  width: 530px;
  height: 100px;
  justify-content: center;
  column-gap: 17px;
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
  position: relative;
}
.title-hint h2::before, .title-hint h2::after {
  content: "";
  width: 20px;
  height: 20px;
  background-image: url(../assets/scratch2.svg);
  display: block;
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;

}

.title-hint h2::before {
  position: absolute;
  left: -26px;
  bottom: 12px;
}

.title-hint h2::after {
  position: absolute;
  right: -26px;
  bottom: 12px;

}

.title-hint p {
font-weight: 700;
color: #333232;
margin-top: 8px;
font-size: 17px;
}
/* 새로운 영역: 캔버스와 컨텐츠 겹치게 */
.scratch-area {
  position: relative;
  width: 550px;
  height: 100px;
  margin: 20px 0 10px 0;
  transform: translateY(-6px);
}

/* 긁기 캔버스 */
canvas {
  width: 550px;
  height: 100px;
  position: absolute;
  bottom: 0;
  left: 0;
  z-index: 2;
  cursor: pointer;
  border-radius: 70px;
}

/* 긁기 완료 후 사라짐 */
canvas.fade-out {
  opacity: 0;
  transition: opacity 0.8s ease;
}
canvas.cleared {
  display: none;
}

.canvas-explanation {

}

/* 긁은 후 나타나는 내용 */
.content {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 550px;
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
  border-radius: 70px;
}

.content h3 {
  order: 2;
  font-size: 20px;
  margin-top: 10px;
  color: #37618c;
  font-weight: 800;
}

.content p {
  order: 1;
  font-size: 15px;
  margin-top: 0;
  color: #636970;
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
}

.explanation {
  position: absolute;
  bottom: 11px;
  font-size: 13px;
  right: 85px;
  background-color: #fff;
  width: 230px;
  text-align: center;
  border-radius: 11px;
  font-weight: 500;
  height: 19px;
}

</style>
