<template>
  <div>
    <h1>Finding...</h1>
    <p>Simply type to search. Searching for:</p>
    <ul>
      <li v-for="(value, key) in searchStrings.value" :class="{ found: value.matches }" :key="key">{{ key }}</li>
    </ul>
    <button @click="goToHomePage">Go Back</button>
    <button @click="resetState">Reset</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  data() {
    return {
      inputSequence: '' as string,
    };
  },
  computed: {
    searchStrings(): Record {
      const strings = this.$route.query.strings as string;
      if (strings) {
        let strarr = strings.split(',').map(decodeURIComponent) as string[];
        console.log(strarr);
        return ref(strarr
          .reduce((acc, curr) => {
            console.log(curr)
            acc[curr] = { matches: false };
            return acc;
          }, {} as Record));
      }
      return [];
    },
    caseSensitive(): bool {
      return this.$route.query.case as bool;
    },
    sticky(): bool{
      return this.$route.query.sticky as bool;
    }
  },
  methods: {
    goToHomePage(): void {
      this.$router.push({ name: 'Home' });
    },
    actFound(key: string, index: number): void {
      this.searchStrings.value[key]["matches"] = true;
      var audio = new Audio("public/trigger-sound.wav");
      audio.play();
      if (!this.sticky) {
        this.inputSequence = this.inputSequence.slice(0, index) + this.inputSequence.slice(index+1);
      }
    },
    resetState(): void {
      for (var [key, value] of Object.entries(this.searchStrings.value)) {
        value["matches"] = false;
      }
      this.inputSequence = "";
    },
    handleKeyPress(event: KeyboardEvent): void {
      if (this.caseSensitive) {
        this.inputSequence += event.key;
      } else {
        this.inputSequence += event.key.toLowerCase();
      }

      for (var [key, value] of Object.entries(this.searchStrings.value)) {
        let indx =this.inputSequence.search(key);
        value["matches"] = indx >= 0;
        if (indx >= 0) {
          this.actFound(key, indx);
        }
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
