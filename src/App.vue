<template>
  <div class="container mx-auto h-screen overflow-hidden">
    <h1 class="text-5xl text-center m-4">Hasmo Chat</h1>
    <div v-if="start" class="container mx-auto h-screen">
      <LoginPage v-if="loggingIn" @login="login" />
      <ChatPage v-if="!loggingIn" />
    </div>
    <div v-if="!start" class="w-5/6 h-full grid place-items-center mx-auto">
      <!-- <img src="@/assets/images/loading-gif.gif" alt="Loading..." class="w-full mx-auto" /> -->
      <progress class="progress progress-warning w-full mx-auto"></progress>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import url from '@/assets/CONSTANTS';
import LoginPage from './components/LoginPage.vue';
import ChatPage from './components/ChatPage.vue';

const URL = `http${url.includes('localhost') ? '' : 's'}://${url}`;

const loggingIn = ref(true);
const start = ref(false);

const login = () => {
  loggingIn.value = false;
};

axios.get(URL).then(({ data: json }) => {
  if (json.message) {
    start.value = true;
  }
});
</script>

<style lang="scss"></style>
