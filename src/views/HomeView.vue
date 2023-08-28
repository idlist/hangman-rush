<script setup lang="ts">
import { ref, computed } from 'vue'

import iconTick from '@/assets/tick.svg'
import iconHide from '@/assets/hide.svg'
import iconShow from '@/assets/show.svg'
import iconRemove from '@/assets/remove.svg'
import iconAdd from '@/assets/add.svg'
import iconClear from '@/assets/clear.svg'
import iconSort from '@/assets/sort.svg'
import iconCopy from '@/assets/copy.svg'

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
  },
])

const toggleGuessed = (i: number) => {
  entries.value[i].guessed = !entries.value[i].guessed
}

const toggleHided = (i: number) => {
  entries.value[i].hided = !entries.value[i].hided
}

const removeEntry = (i: number) => {
  entries.value.splice(i, 1)
}

const addEntry = () => {
  entries.value.push({
    content: '',
    guessed: false,
    hided: false,
  })
}

const clearEntries = () => {
  entries.value.forEach((e) => {
    e.content = ''
    e.guessed = false
    e.hided = false
  })
}

const removeAllEntries = () => {
  entries.value.splice(0)
  addEntry()
}

const allGuessed = ref<boolean>(false)

const toggleAllGuessed = () => {
  const next = !allGuessed.value
  allGuessed.value = next
  entries.value.forEach((e) => { e.guessed = next })
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

const opened = ref<string>('')

const clearOpened = () => {
  opened.value = ''
}

// https://en.wikipedia.org/wiki/Whitespace_character
const Whitespaces = [
  '\u0009', '\u0020', '\u00A0', '\u1680',
  '\u2000', '\u2001', '\u2002', '\u2003', '\u2004',
  '\u2005', '\u2006', '\u2007', '\u2008', '\u2009',
  '\u200A', '\u202F', '\u205F', '\u3000',
]

const output = computed(() => {
  let result = `已猜：${opened.value}\n`
  const checkers = opened.value.toLowerCase()

  entries.value.forEach((e, i) => {
    if (e.hided || e.content === '') {
      return
    }

    let segment = e.guessed ? '√. ' : `${i + 1}. `

    for (const letter of e.content) {
      if (checkers.includes(letter.toLowerCase())) {
        segment += letter
      } else if (Whitespaces.includes(letter)) {
        segment += letter
      } else {
        segment += '*'
      }
    }

    result += `${segment}\n`
  })

  return result.trim()
})

const copyToClipboard = () => {
  navigator.clipboard.writeText(output.value)
}
</script>

<template>
  <div class="hangman-list">
    <div class="hangman-list__title">列表</div>

    <div class="hangman-item actions">
      <button class="btn-add" @click="() => { addEntry() }">
        <img class="btn-add__icon" :src="iconAdd" alt="add" />
        新增
      </button>
      <div class="hangman-item__blank"></div>
      <button class="btn-clear" @click="() => { clearEntries() }">
        <img class="btn-clear__icon" :src="iconClear" alt="remove all" />
        清空
      </button>
      <button class="btn-remove-all" @click="() => { removeAllEntries() }">
        <img class="btn-remove-all__icon" :src="iconRemove" alt="remove all" />
        删除全部
      </button>
    </div>

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
          class="btn-remove"
          @click="() => { removeEntry(i) }">
          <img class="btn-remove__icon" :src="iconRemove" alt="remove" />
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
          <img class="btn-sort__icon" :src="iconSort" alt="remove all" />
          按长度排序
        </button>
      </div>
    </div>
  </div>

  <div class="hangman-guess">
    <div class="hangman-guess__title">猜测</div>

    <div class="hangman-item__input">
      <input
        type="text"
        v-model="opened"
        placeholder="这里的字母已经猜过了……" />
    </div>

    <div class="hangman-item">
      <button class="btn-copy" @click="() => { copyToClipboard() }">
        <img class="btn-copy__icon" :src="iconCopy" alt="remove all" />
        复制到剪贴板
      </button>
      <div class="hangman-item__blank"></div>
      <button class="btn-clear" @click="() => { clearOpened() }">
        <img class="btn-clear__icon" :src="iconClear" alt="remove all" />
        清空
      </button>
    </div>
  </div>

  <div class="hangman-output">
    <div class="hangman-output__title">输出</div>

    <pre class="hangman-output__pre">{{ output }}</pre>
  </div>
</template>

<style lang="sass" scoped>
@use '../main'
@use '../mixins'

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
  padding: 0.125rem 0.25rem
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

  &.actions
    margin-top: 0

  &.title-line
    font-size: var(--font-size-sm)

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
    width: 0
    height: calc(2rem - 2px)

  &__select-all
    font-size: var(--font-size-sm)

%btn
  height: 2rem
  width: 2rem
  display: flex
  align-items: center
  justify-content: center
  column-gap: 0.25rem
  color: var(--color-white)
  margin: 0
  padding: 0.25rem 0.375rem

%btn__icon-md
  width: 1.5rem
  height: 1.5rem

%btn__icon-sm
  width: 1.25rem
  height: 1.25rem

.btn-add
  @extend %btn
  @include mixins.btn-color-sub
  width: auto

  &__icon
    @extend %btn__icon-sm

.btn-guessed
  @extend %btn

  &.guessed
    background-color: var(--color-sub)

  &__icon
    @extend %btn__icon-md

.btn-hided
  @extend %btn

  &.hided
    background-color: var(--color-gray)

  &__icon
    @extend %btn__icon-md

.btn-remove
  @extend %btn
  @include mixins.btn-color-red

  &__icon
    @extend %btn__icon-sm

.btn-remove-all
  @extend .btn-remove
  width: auto

  &__icon
    @extend .btn-remove__icon

.btn-clear, .btn-copy
  @extend %btn
  @include mixins.btn-color-main
  width: auto

  &__icon
    @extend %btn__icon-sm

.btn-sort
  @extend %btn
  @include mixins.btn-color-main
  width: auto

  &__icon
    @extend %btn__icon-md

.hangman-guess
  @extend %card

  &__title
    @extend %card-title

.hangman-output
  @extend %card

  &__title
    @extend %card-title

  &__pre
    margin: 0
    background-color: var(--color-gray-lighter)
    border-radius: 0.25rem
    padding: 0.5rem
    font-size: var(--font-size-sm)
</style>
