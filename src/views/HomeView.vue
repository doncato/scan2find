<template>
  <main>
   <div>
    <div v-for="(field, index) in textFields" :key="index" class="field">
      <input
        v-model="textFields[index]"
        @input="validateInput(index)"
        type="text"
        placeholder="Enter Search Term"
        autofocus
      />
      <button v-if="index+1 == textFields.length" @click="addField">+</button>
      <button v-if="index+1 != textFields.length" @click="delField(index)">-</button>
    </div>

    <button @click="goToFindPage" :disabled="!isFormValid">Go</button>
  </div>
  </main>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  data() {
    return {
      textFields: [''] as string[]
    };
  },
  computed: {
    isFormValid(): boolean {
      return this.textFields.every(field => /^[a-zA-Z0-9]*$/.test(field));
    }
  },
  methods: {
    addField(): void {
      this.textFields.push('');
    },
    delField(i: number): void {
      this.textFields.splice(i, 1);
    },
    validateInput(index: number): void {
      if (!/^[a-zA-Z0-9 ;\-\{\}\(\)]*$/.test(this.textFields[index])) {
        this.textFields[index] = this.textFields[index].slice(0, -1);
      }
    },
    goToFindPage(): void {
      const queryString = this.textFields.map(encodeURIComponent).join(',');
      this.$router.push({ name: 'Find', query: { strings: queryString } });
    }
  }
});
</script>
