<template>
  <v-container
    style="height: 100vh"
    class="d-flex flex-column justify-space-between"
  >
    <v-row>
      <v-col cols="12">
        <!-- Messages Display in Individual Cards -->
        <div ref="messagesContainer" id="messages-container">
          <v-card
            v-for="(msg, index) in messages"
            :key="index"
            class="mb-4"
            variant="tonal"
            color="primary"
          >
            <v-card-text>{{ msg }}</v-card-text>
          </v-card>
          <v-card
            v-if="messages.length === 0"
            :key="index"
            class="mb-4"
            variant="tonal"
            color="primary"
          >
            <v-card-text>Build a python game</v-card-text>
          </v-card>
          <v-card
            v-if="messages.length === 0"
            :key="index"
            class="mb-4"
            variant="tonal"
            color="primary"
          >
            <v-card-text>Help me write a menifesto</v-card-text>
          </v-card>
        </div>

        <!-- Input Area at the Bottom -->
      </v-col>
    </v-row>
    <v-row class="flex-grow-0">
      <v-col cols="12">
        <v-text-field
          v-model="message"
          label="Enter your message"
          variant="outlined"
          append-icon="mdi-send"
          @click:append="sendMessage"
          @keyup.enter="sendMessage"
          single-line
          auto-grow
        ></v-text-field>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref, watch, nextTick } from "vue";

const message = ref("");
const messages = ref([]);
const messagesContainer = ref(null);

function sendMessage() {
  if (message.value.trim()) {
    messages.value.push(message.value);
    message.value = "";
  }
}

watch(
  messages,
  () => {
    //scroll to the bottom of the messages container
    nextTick(() => {
      if (messagesContainer.value) {
        messagesContainer.value.scrollTop =
          messagesContainer.value.scrollHeight;
      }
    });
  },
  { deep: true },
);
</script>

<style>
#messages-container {
  max-height: 80vh;
  overflow-y: auto;
}
</style>
