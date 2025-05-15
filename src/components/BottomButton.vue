<script setup>
import './css/bottomButton.css'
</script>

<template>
  <aside id="aside">
    <div class="aside-box">
      <router-link to="/sunny">
        <button>
          <img src="https://placehold.co/30x30?text=sunny" alt="sunny" class="a-icon" />
        </button>
      </router-link>
      <router-link to="/cloudy">
      <button>
        <img src="https://placehold.co/30x30?text=cloudy" alt="cloudy" class="a-icon" />
      </button>
      </router-link>
      <router-link to="/rainy">
        <button>
          <img src="https://placehold.co/30x30?text=rainy" alt="rainy" class="a-icon" />
        </button>
      </router-link>
      <router-link to="/snowy">
        <button>
          <img src="https://placehold.co/30x30?text=snowy" alt="snowy" class="a-icon" />
        </button>
      </router-link>
       <router-link to="/heart">
        <button>
          <img src="https://placehold.co/30x30?text=heart" alt="heart" class="a-icon" />
        </button>
      </router-link>
       <router-link to="/chat">
        <button>
          <img src="https://placehold.co/30x30?text=chat" alt="chat" class="a-icon" />
        </button>
      </router-link>
    </div>
  </aside>
</template>