<script setup>
import {ref, watch} from 'vue'

const messages = ref([])

const alertMessage = ref('')
const newMessage = ref('')

function sendMessage() {
  if (newMessage.value.trim()) {
    messages.value.push(newMessage.value)
    newMessage.value = ''
  }
}

function deleteMessage(index) {
  messages.value.splice(index, 1)
}

function showMessage(message, index) {
  alertMessage.value = message;
  setTimeout(()=>{
    alertMessage.value = '';
    deleteMessage(index);
  }, 5000)
}

watch(messages, (newX) => {
  if (messages.value.length) {
    messages.value.forEach((message, index) => {
      showMessage(message, index);
    })
  }
})
</script>

<template>
  <main class="d-flex justify-content-center align-items-center min-vh-100 bg-light">
    <div class="card shadow p-4" style="width: 500px;">
      <div class="alert alert-success d-flex gap-2 align-items-center mb-4">
        <button type="button" class="btn-close btn-sm" @click="alertMessage = ''"/>
        <span>{{ alertMessage }}</span>
      </div>

      <div class="border rounded p-3 mb-4" style="height: 200px; overflow-y: auto; background-color: #f8f9fa;">
        <div v-for="(msg, index) in messages" :key="index"
             class="d-flex justify-content-between align-items-center mb-1">
          <span>
            <button type="button" class="btn-close btn-sm" @click="deleteMessage(index)"/>
            {{ msg}}
          </span>
        </div>
      </div>

      <div class="d-flex gap-2">
        <input @keydown.enter="sendMessage" type="text" class="form-control" v-model="newMessage" placeholder="Новое сообщение"/>
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
