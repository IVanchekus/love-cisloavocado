<template>
  <div>
    <div class="main-place">
      <div class="main-place__content">
        <div class="main-place__text-content">
          <div class="main-place__text">With love</div>
          <div class="main-place__text">for Maria</div>
        </div>
      </div>
      <FooterLogo class="main-place__footer-logo"/>
    </div>
    <HeartComponent :offset-x="200"/>
  </div>
</template>

<script lang="ts" setup>
import HeartComponent from '@/components/HeartComponent/HeartComponent.vue'
import FooterLogo from '@/components/FooterLogo/FooterLogo.vue';
</script>

<style scoped lang="scss">
.main-place {
  position: relative;
  height: 100vh;

  &__text-content {
    position: absolute;
    z-index: 2;
    left: 40%;
    top: 50%;
    transform: translate(-50%, -50%);
    max-width: 90%;
    max-height: 90vh;
  }

  &__footer-logo {
    position: absolute;
    z-index: 3;
    left: 50%;
    top: 92%;
    transform: translate(-50%, -50%);
    opacity: 0.5;
  }

  &__text {
    color: white;
    text-align: center;
    font-size: 80px;
    font-weight: 800;
  }

  @media (max-width: 768px) {
    &__text-content {
      left: 10%;
      transform: translateY(-50%);
    }

    &__text {
      text-align: left;
      font-size: 50px;
    }
  }
}


</style>
