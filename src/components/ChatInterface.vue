<script setup lang="ts">
import { ref, onMounted, nextTick } from 'vue'

interface Message {
  id: number
  text: string
  sender: 'user' | 'bot'
}

const messages = ref<Message[]>([])
const newMessage = ref('')
const status = ref('Idle')
const statusUpdated = ref(false)
const chatHistory = ref<HTMLElement | null>(null)

const sendMessage = async () => {
  if (newMessage.value.trim() === '') return

  messages.value.push({
    id: Date.now(),
    text: newMessage.value,
    sender: 'user'
  })

  newMessage.value = ''

  await nextTick()
  if (chatHistory.value) {
    chatHistory.value.scrollTop = chatHistory.value.scrollHeight
  }

  setTimeout(async () => {
    messages.value.push({
      id: Date.now(),
      text: 'This is a simulated response.',
      sender: 'bot'
    })

    await nextTick()
    if (chatHistory.value) {
      chatHistory.value.scrollTop = chatHistory.value.scrollHeight
    }

    await updateStatus()
  }, 1000)
}

const updateStatus = async () => {
  status.value = `Last updated: ${new Date().toLocaleTimeString()}`
  statusUpdated.value = true
  setTimeout(() => {
    statusUpdated.value = false
  }, 1000)
}

onMounted(() => {
  updateStatus()
})
</script>

<template>
  <div class="chat-container">
    <div class="chat-interface">
      <div class="chat-column">
        <div class="chat-history" ref="chatHistory">
          <div v-for="message in messages" :key="message.id" :class="['message', message.sender]">
            {{ message.text }}
          </div>
        </div>
        <div class="chat-input">
          <input v-model="newMessage" @keyup.enter="sendMessage" placeholder="Type your message...">
          <button @click="sendMessage">Send</button>
        </div>
      </div>
      <div class="status-column">
        <div class="status-box" :class="{ 'status-updated': statusUpdated }">
          <h3>Status</h3>
          <p>{{ status }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.chat-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(135deg, #f6f9fc 0%, #e9f1f7 100%);
  padding: 2rem;
}

.chat-interface {
  display: flex;
  gap: 1.5rem;
  height: 80vh;
  width: 90vw;
  max-width: 1200px;
  background: white;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.1), 0 10px 30px rgba(0, 0, 0, 0.08);
  padding: 2rem;
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.chat-column {
  flex: 2;
  display: flex;
  flex-direction: column;
}

.chat-history {
  flex-grow: 1;
  overflow-y: auto;
  border-radius: 16px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  background-color: #f8f9fa;
  box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.chat-history:hover {
  background-color: #f1f3f5;
  box-shadow: inset 0 2px 15px rgba(0, 0, 0, 0.08);
}

.message {
  margin-bottom: 1rem;
  padding: 0.75rem 1rem;
  border-radius: 16px;
  max-width: 70%;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
  line-height: 1.4;
  font-size: 0.95rem;
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  word-wrap: break-word;
  min-width: 60px;
}

.user {
  background-color: #3498db;
  color: white;
  align-self: flex-end;
  margin-left: auto;
  animation: slideInRight 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55) forwards;
}

.bot {
  background-color: #2ecc71;
  color: white;
  animation: slideInLeft 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55) forwards;
}

@keyframes slideInRight {
  from { opacity: 0; transform: translateX(100px) scale(0.8); }
  to { opacity: 1; transform: translateX(0) scale(1); }
}

@keyframes slideInLeft {
  from { opacity: 0; transform: translateX(-100px) scale(0.8); }
  to { opacity: 1; transform: translateX(0) scale(1); }
}

.chat-input {
  display: flex;
  gap: 1rem;
}

.chat-input input {
  flex: 1;
  padding: 1rem 1.5rem;
  border: none;
  border-radius: 30px;
  font-size: 1rem;
  background-color: #f1f3f5;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
}

.chat-input input:focus {
  outline: none;
  box-shadow: 0 5px 20px rgba(52, 152, 219, 0.3);
  transform: translateY(-2px);
}

.chat-input button {
  padding: 1rem 2rem;
  border: none;
  border-radius: 30px;
  font-size: 1rem;
  background-color: #3498db;
  color: white;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 15px rgba(52, 152, 219, 0.3);
}

.chat-input button:hover {
  background-color: #2980b9;
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(52, 152, 219, 0.4);
}

.chat-input button:active {
  transform: translateY(0);
  box-shadow: 0 2px 10px rgba(52, 152, 219, 0.2);
}

.status-column {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.status-box {
  border-radius: 16px;
  padding: 1.5rem;
  background-color: #f8f9fa;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.status-box h3 {
  margin-bottom: 0.75rem;
  color: #333;
  font-size: 1.2rem;
}

.status-box p {
  color: #666;
  font-size: 1rem;
}

.status-updated {
  animation: epicPulse 1.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

@keyframes epicPulse {
  0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(52, 152, 219, 0.7); }
  50% { transform: scale(1.05); box-shadow: 0 0 0 20px rgba(52, 152, 219, 0); }
  100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(52, 152, 219, 0); }
}

@media (max-width: 1024px) {
  .chat-interface {
    flex-direction: column;
    height: 90vh;
    width: 95vw;
  }

  .status-column {
    order: -1;
    margin-bottom: 1.5rem;
  }

  .chat-history {
    height: 60vh;
  }
}
</style>