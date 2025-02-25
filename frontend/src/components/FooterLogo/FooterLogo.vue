<template>
  <div class="footer-logo">
    <img src="@/assets/img/grey_logo.svg" alt="logo" />
    <div class="footer-logo__naming">«Число Авокадо» 2025</div>
  </div>
</template>

<style scoped lang="scss">
.footer-logo {
  display: flex;
  flex-direction: column;
  align-items: center;

  &__naming {
    font-weight: 500;
    color: #4F4F4F;
    font-size: 20px;
  }

  @media (max-width: 768px) {
    &__naming {
      font-size: 14px;
    }
  }
}
</style>


