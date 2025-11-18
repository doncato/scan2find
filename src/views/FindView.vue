<template>
  <div>
    <h1>Next Page</h1>
    <p>Empfangene Strings:</p>
    <ul>
      <li v-for="(string, index) in decodedStrings" :key="index">{{ string }}</li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  computed: {
    decodedStrings(): string[] {
      const strings = this.$route.query.strings as string;
      if (strings) {
        return strings.split(',').map(decodeURIComponent);
      }
      return [];
    }
  }
});
</script>
