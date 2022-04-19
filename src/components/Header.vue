<script setup lang="ts">
import { toggleDark } from '~/composables'

const { t, availableLocales, locale } = useI18n()

const toggleLocales = () => {
  // change to some real logic
  const locales = availableLocales
  locale.value = locales[(locales.indexOf(locale.value) + 1) % locales.length]
}
</script>

<template>
  <div class="header" text-xl mt-6>
    <div class="header-wrapper">
      <router-link class="icon-btn mx-2" float="left" to="/" >
        <h1>What's this color?</h1>
      </router-link>


      <a class="icon-btn mx-2" float="right" :title="t('button.toggle_langs')" @click="toggleLocales">
        <!--      <div i-carbon-language />-->

        <div>{{t('button.toggle_langs')}}: {{t('locales.'+locale)}}</div>

      </a>
    </div>
  </div>
</template>
