<script setup lang="ts">
import * as Nostr from "nostr-tools";
// define property
defineProps<{ tag: Array<string> }>()

</script>

<template>
  <div v-if="tag[0] === 'e' && tag[3] === 'root'">root reply : <a :href="'https://nostx.shino3.net/' + Nostr.nip19.noteEncode(tag[1])">{{ tag[1] }}</a></div>
  <div v-if="tag[0] === 'e' && tag[3] === 'reply'">sub reply : <a :href="'https://nostx.shino3.net/' + Nostr.nip19.noteEncode(tag[1])">{{ tag[1] }}</a></div>
  <div v-if="tag[0] === 'p'">pubkey : <a :href="'https://nostx.shino3.net/' + Nostr.nip19.npubEncode(tag[1])">{{ tag[1] }}</a></div>
  <div v-if="tag[0] === 'r'">related : <a :href="tag[1]">{{ tag[1] }}</a></div>
  <div v-if="tag[0] === 't'">hashtag : #{{ tag[1] }}</div>
</template>

<style scoped></style>
