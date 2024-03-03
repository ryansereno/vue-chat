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
            :variant="msg.role === 'system' ? 'outlined' : 'flat'"
          >
            <v-card-text>{{ msg.content }}</v-card-text>
          </v-card>
        </div>
      </v-col>
    </v-row>

    <v-row
      v-if="messages.length === 0"
      class="justify-space-between flex-grow-0"
    >
      <v-spacer></v-spacer>
      <v-card
        :key="index"
        class="ma-4"
        variant="tonal"
        color="primary"
        @click="sendMessage"
      >
        <v-card-text>Build a python game</v-card-text>
      </v-card>
      <v-card
        :key="index"
        class="ma-4"
        variant="tonal"
        color="primary"
        @click="sendMessage"
      >
        <v-card-text>Help me write a menifesto</v-card-text>
      </v-card>
      <v-card
        :key="index"
        class="ma-4"
        variant="tonal"
        color="primary"
        @click="sendMessage"
      >
        <v-card-text>Assist in a task</v-card-text>
      </v-card>
      <v-spacer></v-spacer>
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
import axios from "axios";

const messagesContainer = ref(null);
const message = ref("");

const messages = ref([]);

const openAIKey = "";

async function fetchOpenAIResponse(userMessage) {
  try {
    const payload = {
      model: "gpt-3.5-turbo",
      messages: [...messages.value, { role: "user", content: userMessage }],
      stream: true,
    };

    const response = await axios.post(
      "https://api.openai.com/v1/chat/completions",
      payload,
      {
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${openAIKey}`,
        },
      },
    );

    return response.data.choices[0].message.content;
  } catch (error) {
    console.error("Error fetching OpenAI response:", error);
    return "Sorry, I couldn't process that.";
  }
}

async function sendMessage() {
  if (message.value.trim()) {
    // Append user message to the chat history
    messages.value.push({ role: "user", content: message.value });

    // Fetch LLM response and append it to the chat history
    const aiContent = await fetchOpenAIResponse(message.value);
    messages.value.push({ role: "system", content: aiContent });

    message.value = ""; // Clear the input after sending
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
