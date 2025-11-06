<script setup>
  import { ref } from 'vue'
  import Datepicker from '@/components/Datepicker.vue'

  const selectedDate = ref('')

  const selectedLang = ref('ru')
</script>

<template>
  <div class="home__container">
    <div class="home__lang-btns-wrap">
      <label class="home__lang-btn">
        <input type="radio" v-model="selectedLang" value="ru" />
        Русский
      </label>
      <label class="home__lang-btn">
        <input type="radio" v-model="selectedLang" value="en" />
        Английский
      </label>
    </div>

    <input
      v-model="selectedDate"
      placeholder="Введите дату в формате ГГГГ-ММ-ДД"
      type="text"
      class="home__input"
    />

    <Datepicker
      v-model="selectedDate"
      :lang="selectedLang"
    />

  </div>
</template>

<style lang="scss" scoped>
.home {
  &__container {
    width: 100%;
    max-width: 500px;
    margin: auto;
    padding: 32px 16px;
  }

  &__lang-btns-wrap {
    margin-bottom: 24px;
  }

  &__lang-btn {
    display: block;
    margin-bottom: 4px;
  }

  &__input {
    margin-bottom: 24px;
    width: 350px;
    padding: 10px 16px;
    outline: 0;
    border: 1px solid $borders-color;
    border-radius: $controls-border-radius;
    transition: $transition;

    &:hover {
      border-color: $primary;
    }
  }
}
</style>
