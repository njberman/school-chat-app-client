<template>
  <div class="container mx-auto h-screen">
    <h1 class="text-5xl text-center m-4">Hasmo Chat</h1>
    <LoginPage v-if="loggingIn" @login="login" />
    <ChatPage v-if="!loggingIn" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import LoginPage from './components/LoginPage.vue';
import ChatPage from './components/ChatPage.vue';

const loggingIn = ref(true);

const login = () => {
  loggingIn.value = false;
};

axios.get('https://school-chat-app-server.onrender.com');
</script>

<style lang="scss"></style>
