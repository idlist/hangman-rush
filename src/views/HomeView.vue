<script setup lang="ts">
import { ref } from 'vue'
import iconTick from '@/assets/tick.svg'
import iconHide from '@/assets/hide.svg'
import iconShow from '@/assets/show.svg'
import iconDelete from '@/assets/delete.svg'
import iconAdd from '@/assets/add.svg'

interface HangmanEntry {
  content: string
  guessed: boolean
  hided: boolean
}

const entries = ref<HangmanEntry[]>([
  {
    content: '',
    guessed: false,
    hided: false,
  }
])

const toggleGuessed = (i: number) => {
  entries.value[i].guessed = !entries.value[i].guessed
}

const toggleHided = (i: number) => {
  entries.value[i].hided = !entries.value[i].hided
}

const deleteEntry = (i: number) => {
  entries.value.splice(i, 1)
}

const addEntry = () => {
  entries.value.push({
    content: '',
    guessed: false,
    hided: false,
  })
}

const allGuessed = ref<boolean>(false)

const toggleAllGuessed = () => {
  const next = !allGuessed.value
  allGuessed.value = next
  entries.value.forEach((i) => { i.guessed = next })
}

const sortEntries = () => {
  entries.value.sort((a, b) => {
    if (a.content.length != b.content.length) {
      return a.content.length - b.content.length
    } else {
      return a.content < b.content ? -1 : 1
    }
  })
}
</script>

<template>
  <div class="hangman-list">
    <div class="hangman-list__title">列表</div>

    <div class="hangman-item title-line">
      <div class="hangman-item__no"></div>
      <div class="hangman-item__btn">猜中</div>
      <div class="hangman-item__blank"></div>
      <div class="hangman-item__btn">隐藏</div>
      <div class="hangman-item__btn">删除</div>
    </div>

    <div class="hangman-item" v-for="(entry, i) of entries" :key="i">
      <div class="hangman-item__no">{{ i + 1 }}.</div>
      <div class="hangman-item__btn">
        <button
          class="btn-guessed"
          :class="{ 'guessed': entry.guessed }"
          @click="() => { toggleGuessed(i) }">
          <img
            v-if="entry.guessed"
            class="btn-guessed__icon"
            :src="iconTick"
            alt="tick" />
        </button>
      </div>
      <div class="hangman-item__input">
        <input type="text" v-model="entry.content" />
      </div>
      <div class="hangman-item__btn">
        <button
          class="btn-hided"
          :class="{ 'hided': entry.hided }"
          @click="() => { toggleHided(i) }">
          <img
            class="btn-hided__icon"
            :src="entry.hided ? iconShow : iconHide"
            :alt="entry.hided ? 'show' : 'hide'" />
        </button>
      </div>
      <div class="hangman-item__btn">
        <button
          class="btn-delete"
          @click="() => { deleteEntry(i) }">
          <img class="btn-delete__icon" :src="iconDelete" />
        </button>
      </div>
    </div>

    <div class="hangman-item">
      <div class="hangman-item__no"></div>
      <div class="hangman-item__btn">
        <button
          class="btn-guessed"
          :class="{ 'guessed': allGuessed }"
          @click="() => { toggleAllGuessed() }">
          <img
            v-if="allGuessed"
            class="btn-guessed__icon"
            :src="iconTick"
            alt="tick" />
        </button>
      </div>
      <div class="hangman-item__select-all">全选</div>
      <div class="hangman-item__blank"></div>
      <div class="hangman-item__sort">
        <button class="btn-sort" @click="() => { sortEntries() }">
          按长度排序
        </button>
      </div>
      <div class="hangman-item__btn">
        <button class="btn-add" @click="() => { addEntry() }">
          <img class="btn-add__icon" :src="iconAdd" alt="add" />
        </button>
      </div>
    </div>
  </div>

  <div class="hangman-guess">
    <div class="hangman-guess__title">猜测</div>

  </div>

  <div class="hangman-output">
    <div class="hangman-output__title">输出</div>

  </div>
</template>

<style lang="sass" scoped>
@use '../main'

%card
  padding: 1rem
  border: 1px solid var(--color-gray-light)
  border-radius: 0.5rem
  position: relative
  margin: 1rem 0

  @media (max-width: main.$screen-sm)
    padding: 0.5rem
    padding-top: 1rem

%card-title
  position: absolute
  top: -0.75rem
  padding: 0.25rem
  background-color: var(--color-white)

.hangman-list
  @extend %card

  &__title
    @extend %card-title

.hangman-item
  display: flex
  column-gap: 0.5rem
  align-items: center
  margin-top: 0.5rem

  &.title-line
    font-size: var(--font-size-sm)
    margin: 0

  &__no
    width: 1.5rem
    text-align: right

  &__btn
    width: 2rem
    display: flex
    align-items: center
    justify-content: center

  &__blank
    flex-grow: 1

  &__input
    flex-grow: 1
    display: flex

  &__input > input
    flex-grow: 1
    height: calc(2rem - 2px)

  &__select-all
    font-size: var(--font-size-sm)

%btn
  height: 2rem
  width: 2rem
  display: flex
  align-items: center
  justify-content: center

%btn__icon
  width: 1.5rem
  height: 1.5rem

.btn-guessed
  @extend %btn

  &.guessed
    background-color: var(--color-sub)

  &__icon
    @extend %btn__icon

.btn-hided
  @extend %btn

  &.hided
    background-color: var(--color-gray)

  &__icon
    @extend %btn__icon

.btn-delete
  @extend %btn
  border-color: var(--color-red)
  background-color: var(--color-red)

  &:hover
    background-color: var(--color-red-light)

  &:active
    background-color: var(--color-red-lighter)

  &__icon
    width: 1.25rem
    height: 1.25rem

.btn-add
  @extend %btn
  border-color: var(--color-sub)
  background-color: var(--color-sub)

  &:hover
    background-color: var(--color-sub-light)

  &__icon
    @extend %btn__icon

.btn-sort
  height: 2rem
  padding: 0 0.5rem
  display: flex
  justify-content: center
  align-items: center
  color: var(--color-white)
  border-color: var(--color-main)
  background-color: var(--color-main)

  &:hover
    background-color: var(--color-main-light)

  &:active
    background-color: var(--color-main-lighter)

.hangman-guess
  @extend %card

  &__title
    @extend %card-title

.hangman-output
  @extend %card

  &__title
    @extend %card-title
</style>
