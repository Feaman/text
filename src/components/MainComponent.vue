<template>
  <div class="container">
    <div
      @click="textareaContainerOnClick"
      class="textarea-container"
      ref="textareaContainer"
    >
      <textarea
        v-model="text"
        @input.stop="handleListItemTextAreaHeight"
        :style="{ fontSize: `${fontSize}px`, textAlign: textAlign as 'center' }"
        ref="textarea"
        class="textarea"
        placeholder="Начните вводить текст"
      />
    </div>
    <Transition name="fade">
      <div
        v-if="isToolsShown"
        class="tools"
      >
        <img 
          @click="alignText('left')"
          src="../assets/align-left.svg"
          :class="['tool-icon', { 'tool-icon--active': textAlign === TEXT_ALIGN_LEFT }]"
          alt="Align left"
        />
        <img 
          @click="alignText('center')"
          src="../assets/align-center.svg"
          :class="['tool-icon', { 'tool-icon--active': textAlign === TEXT_ALIGN_CENTER }]"
          alt="Align left"
        />
        <img 
          @click="alignText('right')"
          src="../assets/align-right.svg"
          :class="['tool-icon', { 'tool-icon--active': textAlign === TEXT_ALIGN_RIGHT }]"
          alt="Align left"
        />
        <img 
          @click="hideTools()"
          src="../assets/fullscreen.svg"
          :class="['tool-icon']"
          style="fill: #bababa"
          alt="Fullscreen"
        />
        <div
          class="slider"
        >
          <Slider
            v-model="sliderValue"
            @update="changeFontSize"
            :min="1"
            :max="90"
            showTooltip="drag"
            :lazy="false"
          />
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import Slider from '@vueform/slider'

const TEXT_ALIGN_LEFT = 'left'
const TEXT_ALIGN_CENTER = 'center'
const TEXT_ALIGN_RIGHT = 'right'

const text = ref('')
const fontSize = ref(16)
const sliderValue = ref(fontSize.value)
const textAlign = ref(TEXT_ALIGN_CENTER)
const textarea = ref<HTMLTextAreaElement>()
const isToolsShown = ref(true)

// Fix 100vh for mobile
document.documentElement.style.setProperty('--vh', `${window.innerHeight * 0.01}px`)

onMounted(() => {
  textarea.value?.focus()
  handleWindowResize()
  window.addEventListener('resize', handleWindowResize)
})

function textareaContainerOnClick() {
  textarea.value?.focus()
  isToolsShown.value = true
}

function alignText(alignType: string) {
  textAlign.value = alignType
}

function handleWindowResize() {
  // Fix 100vh for mobile
  document.documentElement.style.setProperty('--vh', `${window.innerHeight * 0.01}px`)
}

function handleListItemTextAreaHeight() {
  if (textarea.value) {
    const $textArea = textarea.value
    let textAreaHeight = 0
    $textArea.style.height = '0'
    textAreaHeight = $textArea.scrollHeight
    $textArea.style.height = `${textAreaHeight}px`
  }
}

function changeFontSize(newFontSize: number) {
  fontSize.value = newFontSize
  setTimeout(() => {
    handleListItemTextAreaHeight()
  });
}

function  hideTools() {
  isToolsShown.value = false
}
</script>

<style scoped>
.container {
  width: 100%;
  height: calc(var(--vh, 1vh) * 100);
  max-height: calc(var(--vh, 1vh) * 100);
  display: flex;
  flex-direction: column;
  background-color: #fff;
}

.textarea-container {
  width: 100%;
  max-height: 100%;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: auto;
}

.textarea {
  width: 100%;
  max-height: 100%;
  font-family: Roboto, system-ui, Avenir, Helvetica, Arial, sans-serif;
  border: none;
  resize: none;
  margin: 0;
  padding: 15px;
  outline: 0;
  background-color: #fff;
  color: #000;
  overflow: hidden;
}

.tools {
  width: 100%;
  height: 64px;
  min-height: 64px;
  display: flex;
  align-items: center;
  box-shadow: 0 0 5px #000;
}

.tool-icon {
  width: 32px;
  height: 32px;
  margin: 0 8px;
  box-shadow: 0 0 0 rgba(0, 0, 0, 0);
  padding: 4px;
}

.tool-icon--active {
  box-shadow: 0 0 3px rgba(0, 0, 0, 0.5);
  border-radius: 6px;
}

.slider {
  flex: 1;
  margin: 0 24px 0 16px;
}
</style>
