<script setup>
import {ref, watch} from 'vue'

const messages = ref([])

const alertMessage = ref('')
const newMessage = ref('')

function sendMessage() {
  if (newMessage.value.trim()) {
    messages.value.push({message: newMessage.value, isDeleted: false})
    clearInput();
  }
}

function clearInput() {
  newMessage.value = ''
}

function deleteMessage(message) {
  const index = messages.value.findIndex(emp => emp === message)

  if (index !== -1) {
    messages.value[index].isDeleted = true;
  }
}

function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

const isLoopRunning = ref(false)

async function startLoop() {
  if (isLoopRunning.value) return;

  isLoopRunning.value = true;

  while (messages.value.length > 0) {
    for (const message of messages.value) {
      if (!message.isDeleted) {
        alertMessage.value = message;
        await delay(1000);
      }
    }
  }

  isLoopRunning.value = false;
  alertMessage.value = '';
}

watch(messages, () => {
  if (messages.value.length > 0 && !isLoopRunning.value) {
    startLoop();
  }
}, {deep: true});
</script>

<template>
  <main class="d-flex justify-content-center align-items-center min-vh-100 bg-light">
    <div class="card shadow p-4" style="width: 500px;">
      <div class="alert alert-success d-flex gap-2 align-items-center mb-4">
        <button v-if="alertMessage" type="button" class="btn-close btn-sm" @click="deleteMessage(alertMessage)"/>
        <span>{{ alertMessage.message }}</span>
      </div>

      <div class="border rounded p-3 mb-4" style="height: 200px; overflow-y: auto; background-color: #f8f9fa;">
        <div v-for="(msg, index) in messages" :key="index"
             class="d-flex justify-content-between align-items-center mb-1">
          <span>
            <button v-if="msg.isDeleted" type="button" class="btn-close btn-sm"/>
            {{ msg.message }}
          </span>
        </div>
      </div>

      <div class="d-flex gap-2">
        <input @keydown.enter="sendMessage" type="text" class="form-control" v-model="newMessage"
               placeholder="Новое сообщение"/>
        <button class="btn btn-success px-4" @click="sendMessage">ОТПРАВИТЬ</button>
      </div>
    </div>
  </main>
</template>

<style scoped>
.card {
  border-radius: 12px;
}

input::placeholder {
  color: #adb5bd;
}
</style>
