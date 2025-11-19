<template>
  <div>
    <ul>
      <li v-for="(value, key) in searchStrings" :class="{ found: value.matches }" :key="key" class="search">{{ key }}</li>
    </ul>
    <hr/>
    <button @click="goToHomePage">Go Back</button>
    <button @click="resetState">Reset</button>
    <p class="light">Simply type to search.</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

interface SearchString {
  [key: string]: {
    matches: boolean
  };
}

export default defineComponent({
  data() {
    let searchStrings = {} as SearchString;
    const strings = this.$route.query.strings as string;
    if (strings) {
      let strarr = strings.split(';').map(decodeURIComponent).filter(n => n !== "") as string[];
      searchStrings = strarr
        .reduce((acc, curr) => {
          acc[curr] = {matches: false};
          return acc;
        }, {} as SearchString);
    };
    return {
      inputSequence: '' as string,
      searchStrings: searchStrings,
    };
  },
  computed: {
    caseSensitive(): boolean {
      return this.$route.query.case == 'true';
    },
    sticky(): boolean {
      return this.$route.query.sticky == 'true';
    }
  },
  methods: {
    goToHomePage(): void {
      this.$router.push({ name: 'Home' });
    },
    actFound(key: string, index: number): void {
      const old_value = structuredClone(this.searchStrings[key]?.matches ?? false);
      if (this.searchStrings[key]) {
        this.searchStrings[key].matches = index >= 0;
      }

      if (index < 0) {
        return;
      }

      if (old_value !== this.searchStrings[key]?.matches) {
        var audio = new Audio("public/trigger-sound.wav");
        audio.play();
      }
      if (!this.sticky) {
        this.inputSequence = this.inputSequence.slice(0, index) + this.inputSequence.slice(index+1);
      }
    },
    resetState(): void {
      for (let key in this.searchStrings) {
        if (this.searchStrings[key]) {
          this.searchStrings[key].matches = false;
        }
      }
      this.inputSequence = "";
    },
    handleKeyPress(event: KeyboardEvent): void {
      if (event.key == "Enter") {
        return;
      }
      if (this.caseSensitive) {
        this.inputSequence += event.key;
      } else {
        this.inputSequence += event.key.toLowerCase();
      }

      for (let key in this.searchStrings) {
        var value = this.searchStrings[key] ?? {};
        var indx: number;
        if (!this.caseSensitive) {
          indx = this.inputSequence.search(key.toLowerCase());
        } else {
          indx = this.inputSequence.search(key);
        }
        this.actFound(key, indx);
      }

      if (this.inputSequence.length > 255) {
        this.inputSequence = this.inputSequence.slice(-255);

      }
    },
  },
  mounted: function() {
      window.addEventListener('keypress', this.handleKeyPress);
  },
  unmounted: function() {
      window.removeEventListener('keypress', this.handleKeyPress);
  },
});
</script>
