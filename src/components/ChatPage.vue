<!-- eslint-disable import/no-extraneous-dependencies -->
<!-- eslint-disable import/no-extraneous-dependencies -->
<!-- eslint-disable vuejs-accessibility/form-control-has-label -->
<template>
  <div class="container mx-auto h-full">
    <div class="container mx-auto h-3/4 rounded-lg">
      <div class="text-3xl">
        {{ connected - 1 }} other {{ connected - 1 === 1 ? 'person' : 'people' }} online right now.
      </div>
      <div class="h-5/6 w-full flex flex-col-reverse overflow-auto">
        <div
          class="message h-30 border-solid border-2 border-white m-0.5 p-2 rounded grid grid-cols-2"
          v-for="message in messages"
          :key="message.id"
        >
          <div class="justify-self-start w-3/4">{{ message.user }}: {{ message.message }}</div>
          <div class="justify-self-end w-1/4 text-end">
            {{ message.time }}
          </div>
        </div>
      </div>
      <input
        class="input input-bordered input-primary w-full h-1/6"
        type="text"
        placeholder="Send Message... (must wait 0.5s between sending each message)"
        v-model="messageInput"
        @keypress="sendMessage"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { v4 as uuid } from 'uuid';
import url from '@/assets/CONSTANTS';

const messageInput = ref('');

const messages = ref([]);
const allowedToSend = ref(true);
const connected = ref(0);

const URL = `ws${url.includes('localhost') ? '' : 's'}://${url}`;

const ws = new WebSocket(URL);
ws.onerror = console.error;

ws.onopen = () => {
  console.log('[open] Connection Established to websocket');
  ws.send(`connect ${localStorage.getItem('user')}`);
};

ws.onmessage = (e) => {
  if (e.data[0] === '{') {
    messages.value.unshift(JSON.parse(e.data));
  } else if (e.data.includes('connected')) {
    connected.value = Number(e.data.split(' ')[1]);
  }
};

const sendMessage = (e) => {
  if (e.key === 'Enter') {
    if (ws.readyState === 1 && messageInput.value !== '' && allowedToSend.value) {
      const date = new Date();
      const time = `${date.toLocaleTimeString()} ${date.toLocaleDateString()}`;
      ws.send(
        JSON.stringify({
          user: localStorage.getItem('user'),
          message: messageInput.value,
          time,
          id: uuid(),
        }),
      );
      messageInput.value = '';
      allowedToSend.value = false;
      setTimeout(() => {
        allowedToSend.value = true;
      }, 500);
    }
  }
};
</script>

<style></style>
