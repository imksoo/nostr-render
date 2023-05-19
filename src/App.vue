<script setup lang="ts">
import NostrProfile from './components/NostrProfile.vue';
import NostrEvent from './components/NostrEvent.vue';

import { computed, ref } from 'vue'
import * as Nostr from "nostr-tools";

const eventJSON = ref("")

const event = computed(() => {
  try {
    return JSON.parse(eventJSON.value) as Nostr.Event;
  } catch (error) {
    return null;
  }
})
const kind = computed(() =>{
  if (event.value) {
    return event.value.kind;
  } else {
    return 0;
  }
})
</script>

<template>
  <h1>Nostr event (JSON)</h1>
  <textarea v-model="eventJSON" id="eventJSON"></textarea>
  <div v-if="event">
    <NostrProfile :event="event" v-if="kind===0"></NostrProfile>
    <NostrEvent :event="event" v-if="kind===1"></NostrEvent>
  </div>
</template>

<style scoped>
#eventJSON {
  height: 20em;
  width: 100%;
  overflow: auto;
  font-family: monospace;
  margin: 1em 0px;
}
</style>
