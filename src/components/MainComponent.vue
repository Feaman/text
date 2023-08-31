<template>
  <div class="container">
    <div
      :style="{ fontSize: `${fontSize}px`, justifyContent: textAlign }"
      ref="textarea"
      class="textarea"
      placeholder="Начните вводить текст"
      contenteditable="true"
    >
      {{ text }}
    </div>
    <div class="tools">
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
      <div
        class="slider"
      >
        <Slider
          v-model="sliderValue"
          @update="fontSize = $event"
          :min="1"
          :max="90"
          showTooltip="drag"
          :lazy="false"
        />
      </div>
    </div>
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

// Fix 100vh for mobile
document.documentElement.style.setProperty('--vh', `${window.innerHeight * 0.01}px`)

onMounted(() => {
  textarea.value?.focus()
  handleWindowResize()
  window.addEventListener('resize', handleWindowResize)

  textarea.value?.addEventListener("paste", function(event: ClipboardEvent) {
    event.preventDefault();

    if (event.clipboardData) {
      const content = event?.clipboardData.getData('text/plain');

      if (textarea.value && content) {
        textarea.value.innerText = content;
      }
    }
});
})

function alignText(alignType: string) {
  textAlign.value = alignType
}

function handleWindowResize() {
  // Fix 100vh for mobile
  document.documentElement.style.setProperty('--vh', `${window.innerHeight * 0.01}px`)
}
</script>

<style scoped>
.container {
  width: 100%;
  height: calc(var(--vh, 1vh) * 100);
  display: flex;
  flex-direction: column;
  background-color: #fff;
}

.textarea {
  width: 100%;
  height: calc(100% - 64px);
  display: flex;
  flex-direction: column;
  font-family: Roboto, system-ui, Avenir, Helvetica, Arial, sans-serif;
  border: none;
  resize: none;
  margin: 0;
  padding: 15px;
  outline: 0;
  overflow: auto;
  background-color: #fff;
  color: #000;
  align-items: center;
}

[contenteditable=true]:empty:before{
  content:attr(placeholder);
  color: #ccc;
  font-size: 24px;
}

.tools {
  width: 100%;
  height: 64px;
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
