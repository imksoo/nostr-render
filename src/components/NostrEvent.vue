<script setup lang="ts">
import { computed } from 'vue'
import * as Nostr from "nostr-tools";

// define property
const props = defineProps<{ event: Nostr.Event}>()

const verifySignatureStatus = computed(() => {
  return Nostr.verifySignature(props.event);
})

const created_at_str = computed(()=>{
  return new Date(props.event.created_at * 1000).toLocaleString()
})
</script>

<template>
  <div v-if="event">
    <p>id: {{ event.id }}</p>
    <p>kind : {{ event.kind }}</p>
    <p>pubkey : {{ event.pubkey }}</p>
    <p>content : {{ event.content }}</p>
    <p>created_at : {{ event.created_at }} ({{ created_at_str }})</p>
    <p>signature verify status:
      <span v-if="verifySignatureStatus" class="verify-signature-succeeded">succeeded</span>
      <span v-else class="verify-signature-error">error</span>
    </p>
  </div>
</template>

<style scoped>
.verify-signature-succeeded {
  color: blue;
}
.verify-signature-error {
  color: red;
}
</style>
