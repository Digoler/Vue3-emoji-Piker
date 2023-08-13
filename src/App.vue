<template>
  <div class="emoji">
    <textarea v-model="input"></textarea>

    <emoji-picker @emoji="insert" :emoji-table="emojis" :search="search">
      <template
        class="emoji-invoker"
        #invoker
        slot-scope="{ events: { click: clickEvent } }"
        @click.stop="clickEvent"
      >
        <button type="button">open</button>
      </template>
      <template #picker slot-scope="{ emojis, insert, display }">
        <div>
          <div>
            <input type="text" v-model="search" />
          </div>
          <div>
            <div v-for="(emojiGroup, category) in emojis" :key="category">
              <h5>{{ category }}</h5>
              <div>
                <button
                  v-for="(emoji, emojiName) in emojiGroup"
                  :key="emojiName"
                  @click="insert(emoji)"
                  :title="emojiName"
                >
                  {{ emoji }}
                </button>
              </div>
            </div>
          </div>
        </div>
      </template>
    </emoji-picker>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import emojiPicker from '@/component/emojiPicker.vue'
import emojis from '@/utils/emojis'
let input = ref('')
const search = ref('')
function insert(emoji: any) {
  input += emoji
}
function clickEvent() {
  console.log(132)
}
</script>

<style scoped></style>
