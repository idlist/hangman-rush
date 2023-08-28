<script setup lang="ts">
import iconPersonal from '@/assets/idlist.png'
</script>

<template>
  <div class="about">
    <p class="author">
      由
      <a
        class="author__link"
        href="https://idl.ist"
        target="_blank"
        rel="noopener noreferer">
        <img class="personal-icon" :src="iconPersonal" alt="idlist" />
        i'DLisT
      </a>
      重复造轮
    </p>
    <p>
      <a
        href="https://github.com/idlist/hangman-rush"
        target="_blank"
        rel="noopener noreferer">GitHub 仓库</a>
    </p>
    <p>
      跟风做的开字母小助手，跟的
      <a
        href="https://compilationfail.github.io/guess.html"
        target="_blank"
        rel="noopener noreferer">这个风</a>。
    </p>
    <p>
      <span>想了想算是一种吊死鬼（Hangman），所以取了个这么个标题。</span>
      <span>而 Rush 则是指的抢答的形式。</span>
    </p>
    <p>
      想不到还要写什么了。玩得开心！
    </p>
  </div>
</template>

<style lang="sass" scoped>
.about
  margin-top: 0.5rem

.author
  display: flex
  column-gap: 0.5rem
  align-items: center
  margin: 1rem 0
  color: var(--color-gray-light)

  &__link
    display: flex
    align-items: center
    column-gap: 0.25rem

.personal-icon
  width: 4rem
  height: 4rem
  border: 1px solid var(--color-gray)
  border-radius: 4rem

</style>
