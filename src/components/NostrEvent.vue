<script setup lang="ts">
import { computed } from 'vue'
import * as Nostr from "nostr-tools";
import NostrLink from './NostrLink.vue';
import NostrEventTag from './NostrEventTag.vue';

// define property
const props = defineProps<{ event: Nostr.Event }>()

const verifySignatureStatus = computed(() => {
  return Nostr.verifySignature(props.event);
})

const created_at_str = computed(() => {
  return new Date(props.event.created_at * 1000).toLocaleString();
})

const contentTokens = computed(() => {
  const emojiMap = new Map(props.event.tags.filter((t) => { return t[0] === "emoji" }).map((t) => [t[1], t[2]]));
  const words = props.event.content.split(/(:[a-z0-9_]+:|https?:\/\/[\w\-.~:/?#\[\]@!$&'()*+,;=]+|nostr:(?:nprofile|nrelay|nevent|naddr|nsec|npub|note)[a-z0-9]*)/g);

  const tokens = words.map((word) => {
    if (!word) {
      return;
    }
    if (word.startsWith(":") && word.endsWith(":")) {
      const emojiName = word.slice(1, -1);
      if (emojiMap.has(emojiName)) {
        return { type: "emoji", emojiName: emojiName, src: emojiMap.get(emojiName) };
      }
    } else if (word.startsWith("http")) {
      const url = new URL(word);
      const ext = url.pathname.split(".").pop()?.toLocaleLowerCase() ?? "";
      if (['jpg', 'jpeg', 'png', 'gif', 'svg', 'ico', 'bmp', 'webp'].includes(ext)) {
        return { type: "img", src: word };
      } else {
        return { type: "link", href: word, content: word };
      }
    } else if (word.startsWith('nostr:')) {
      return { type: 'nostr', content: word.replace('nostr:', ''), decode: Nostr.nip19.decode(word.replace('nostr:', '')) };
    } else {
      return { type: 'text', content: word }
    }
  })
  return tokens;
})
</script>

<template>
  <div v-if="event">
    <p>id: {{ event.id }}</p>
    <p>kind : {{ event.kind }}</p>
    <p>pubkey : {{ event.pubkey }}</p>
    <p class="content">
      content:<br />
      <template v-for="(token, index) in contentTokens">
        <span :key="index" v-if="token?.type === 'text'" class="note-text">{{ token.content }}</span>
        <a v-else-if="token?.type === 'link'" :href="token.href" target="_blank">{{ token?.content }}</a>
        <a v-else-if="token?.type === 'img'" :href="token.src" target="_blank"><img :src="token.src" referrerpolicy="no-referrer" class="inline-image"/></a>
        <img v-else-if="token?.type === 'emoji'" :src="token.src" :alt="token.emojiName" referrerpolicy="no-referrer"
          class="emoji">
        <span v-else-if="token?.type === 'nostr'">
          <nostr-link v-if="token.decode" :link="token.decode"></nostr-link>
        </span>
      </template>
    </p>
    <div v-if="event.tags">
      tags:<br />
      <nostr-event-tag v-for="(tag) in event.tags" :tag="tag"></nostr-event-tag>
    </div>
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

.note-text {
  white-space: pre;
  word-wrap: break-word;
  word-break: break-all;
  overflow: hidden;
}

.emoji {
  max-height: 1.5em;
  max-width: 1.5em;
  vertical-align: middle;
  margin: 0.1em;
}

.inline-image {
  max-width: 80%;
  margin: 0.1em;
}
</style>
