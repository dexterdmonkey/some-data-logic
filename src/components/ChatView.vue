<script setup>
import { nextTick, ref } from 'vue';

// Messages handler
const messages = ref([]);
// const appendMessage = (messages, additionalMessages) => {
//   console.log("messages:", messages);
//   console.log("additionalMessages", additionalMessages);

//   const messageIds = [];
//   messages.forEach((message) => {
//     messageIds.push(message.message_id);
//   });
//   console.log("messageIds", messageIds);


//   additionalMessages.forEach((newMessage) => {
//     console.log(" =============!")
//     console.log("newMessage:", newMessage)

//     if (messageIds.includes(newMessage.message_id)) {
//       const idx = messageIds.indexOf(newMessage.message_id);
//       console.log("newMessage.message_id:", newMessage.message_id);
//       console.log("messages[idx]:", messages[idx]);

//       messages[idx].tstamp = newMessage.tstamp;
//       messages[idx].status = newMessage.status;
//     } else {
//       console.log("message_id not in messages, unshift")
//       messages.unshift(newMessage);
//       messageIds.unshift(newMessage.message_id);
//     }
//   });

//   messages.sort((a, b) => b.tstamp - a.tstamp);
//   return messages;
// }

const appendMessage = (messages, additionalMessages) => {
  const messageMap = new Map();

  // Build a Map of existing messages for faster lookups
  messages.forEach((message) => {
    messageMap.set(message.message_id, message);
  });

  additionalMessages.forEach((newMessage) => {
    if (messageMap.has(newMessage.message_id)) {
      // Update existing message
      const existingMessage = messageMap.get(newMessage.message_id);
      existingMessage.tstamp = newMessage.tstamp;
      existingMessage.status = newMessage.status;
    } else {
      // Add new message to the beginning of the array
      messages.unshift(newMessage);
      messageMap.set(newMessage.message_id, newMessage);
    }
  });

  // Sort the messages by tstamp in descending order
  messages.sort((a, b) => b.tstamp - a.tstamp);

  return messages;
};



// Operation
const getInitialMessages = async function () {
  const additionalMessages = [
    { message_id: '4', tstamp: 1000, status: "read", text: "Halo" },
    { message_id: '5', tstamp: 1001, status: "read", text: "Iya ada apa" },
    { message_id: '6', tstamp: 1002, status: "read", text: "mending turu" },
    { message_id: '7', tstamp: 1003, status: "read", text: "kamu sukanya tidur" },
    { message_id: '8', tstamp: 1004, status: "read", text: "iya males" },

  ]

  messages.value = appendMessage(messages.value, additionalMessages);
  await nextTick();

  console.log("after appended by newMessages,")
  console.log("messages:", messages.value)
}


const getPreviousMessages = async function () {
  const additionalMessages = [
    { message_id: '1', tstamp: 997, status: "read", text: "iya" },
    { message_id: '2', tstamp: 998, status: "read", text: "met bobo" },
    { message_id: '3', tstamp: 999, status: "read", text: "met bobo juga" },
    { message_id: '4', tstamp: 1000, status: "read", text: "Halo" },
    { message_id: '5', tstamp: 1001, status: "read", text: "Iya ada apa" },

  ]

  messages.value = appendMessage(messages.value, additionalMessages);
  await nextTick();

  console.log("after appended by newMessages,")
  console.log("messages:", messages.value)
}


const sendMessage = async function () {
  const additionalMessages = [
    { message_id: '9', tstamp: 1005, status: "pending", text: "kamu jangan males dong" },
  ]

  messages.value = appendMessage(messages.value, additionalMessages);
  await nextTick();

  console.log("after appended by newMessages,")
  console.log("messages:", messages.value)
}

const getMessagesAfterSend = async function () {
  const additionalMessages = [
    { message_id: '6', tstamp: 1002, status: "read", text: "mending turu" },
    { message_id: '7', tstamp: 1003, status: "read", text: "kamu sukanya tidur" },
    { message_id: '8', tstamp: 1004, status: "read", text: "iya males" },
    { message_id: '9', tstamp: 1007, status: "read", text: "kamu jangan males dong" },
    { message_id: '10', tstamp: 1010, status: "delivered", text: "ya mau gmn lagi" },
  ]

  messages.value = appendMessage(messages.value, additionalMessages);
  await nextTick();

  console.log("after appended by newMessages,")
  console.log("messages:", messages.value)
}


</script>

<template>
  <main>
    <button @click="getInitialMessages">
      Get Initial
    </button>

    <button @click="getPreviousMessages">
      Get Previous
    </button>

    <button @click="sendMessage">
      Send Message
    </button>

    <button @click="getMessagesAfterSend">
      Get After Send
    </button>

    <div>
      <div v-for="message in messages" v-bind:key="message.message_id">
        [{{ message.message_id }}] - [{{ message.tstamp }}] - {{ message.text }} ({{ message.status }})
      </div>
    </div>
  </main>
</template>
