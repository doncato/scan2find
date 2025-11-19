<template>
  <div>
    <ul>
      <li v-for="(value, key) in searchStrings.value" :class="{ found: value.matches }" :key="key" class="search">{{ key }}</li>
    </ul>
    <hr/>
    <button @click="goToHomePage">Go Back</button>
    <button @click="resetState">Reset</button>
    <p class="light">Simply type to search.</p>
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
        let strarr = strings.split(';').map(decodeURIComponent).filter(n => n !== "") as string[];
        return ref(strarr
          .reduce((acc, curr) => {
            acc[curr] = { matches: false };
            return acc;
          }, {} as Record));
      }
      return [];
    },
    caseSensitive(): bool {
      return this.$route.query.case == 'true';
    },
    sticky(): bool{
      return this.$route.query.sticky == 'true';
    }
  },
  methods: {
    goToHomePage(): void {
      this.$router.push({ name: 'Home' });
    },
    actFound(value: Record, index: number): void {
      const old_value = structuredClone(value["matches"]);
      value["matches"] = index >= 0;

      if (index < 0) {
        return;
      }

      if (old_value !== value["matches"]) {
        var audio = new Audio("public/trigger-sound.wav");
        audio.play();
      }
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
      if (event.key == "Enter") {
        return;
      }
      if (this.caseSensitive) {
        this.inputSequence += event.key;
      } else {
        this.inputSequence += event.key.toLowerCase();
      }

      for (var [key, value] of Object.entries(this.searchStrings.value)) {
        if (!this.caseSensitive) {
          key = key.toLowerCase();
        }
        var indx =this.inputSequence.search(key);
        this.actFound(value, indx);
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
