<script setup lang="ts">
import { computed } from 'vue'
import * as Nostr from "nostr-tools";


// define property
const props = defineProps<{ link: Nostr.nip19.DecodeResult }>()

const url = computed(() => {
  const nostx = 'https://nostx.shino3.net/'
  switch (props.link.type) {
    case "nprofile":
      return nostx + Nostr.nip19.npubEncode(props.link.data.pubkey);
    case "nevent":
      return nostx + Nostr.nip19.noteEncode(props.link.data.id);
    case "note":
      return nostx + Nostr.nip19.noteEncode(props.link.data)
  }
})
</script>

<template>
  <a :href="url" v-bind:class="link.type" target="_blank">{{ link.type }} {{ url }}</a>
</template>

<style scoped>
.note {
  color: blueviolet;
}
.nprofile {
  color: coral;
}
.nevent {
  color: green;
}
</style>
