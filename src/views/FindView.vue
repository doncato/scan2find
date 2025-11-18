<template>
  <div>
    <h1>Finding...</h1>
    <p>Simply type to search. Searching for:</p>
    <ul>
      <li v-for="(string, index) in decodedStrings" :key="index">{{ string }}</li>
    </ul>
    <button @click="goToHomePage">Go Back</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  data() {
    return {
      inputSequence: '' as string,
    };
  },
  computed: {
    decodedStrings(): string[] {
      const strings = this.$route.query.strings as string;
      if (strings) {
        return strings.split(',').map(decodeURIComponent);
      }
      const searchStrings = strings.map(v => v.toLowerCase());
      return [];
    }
  },
  methods: {
    goToHomePage(): void {
      this.$router.push({ name: 'Home' });
    },
    handleKeyPress(event: KeyboardEvent): void {
      this.inputSequence += event.key;

      console.log(this.inputSequence);
      console.log(this.searchStrings.value);

      if (this.searchStrings.some(v => this.inputSequence.includes(v))) {
        console.log("Found");
      }

      if (this.inputSequence.length > 255) {
        this.inputSequence = this.inputSequence.slice(-255);

      }
    },
  },
  created: function() {
      window.addEventListener('keypress', this.handleKeyPress);
  },
  destroyed: function() {
      window.addEventListener('keypress', this.handleKeyPress);
  },
});
</script>
