<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header class="header">
    <nav class="nav">
      <RouterLink class="nav-item" to="/">开字母小助手</RouterLink>
      <RouterLink class="nav-item" to="/about">关于</RouterLink>
    </nav>
  </header>

  <main class="container">
    <RouterView />
  </main>
</template>

<style lang="sass" scoped>
@use './main'

.header
  position: fixed
  top: 0
  left: 0
  right: 0
  display: flex
  align-items: center
  color: var(--color-white)
  background-color: var(--color-main)
  z-index: 1000

.nav
  display: flex
  align-items: center

.container
  max-width: var(--content-width)
  padding: 1rem
  padding-top: 2.5rem

  @media (max-width: main.$screen-sm)
    padding: 0.5rem
    padding-top: 2.5rem

.nav-item
  display: flex
  padding: 0.75rem
  color: var(--color-white)
  text-decoration: none

  &:hover
    background-color: var(--color-main-light)

  &:active
    background-color: var(--color-main-lighter)
</style>
