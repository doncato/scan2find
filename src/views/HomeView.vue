<script setup lang="ts">
import TheWelcome from '../components/TheWelcome.vue'
</script>

<template>
  <main>
   <div>
    <div v-for="(field, index) in textFields" :key="index" class="field">
      <input
        v-model="textFields[index]"
        @input="validateInput(index)"
        type="text"
        placeholder="Geben Sie Ihren Text ein"
      />
      <button @click="addField">+</button>
    </div>

    <button @click="goToNextPage" :disabled="!isFormValid">Go</button>
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
    validateInput(index: number): void {
      if (!/^[a-zA-Z0-9]*$/.test(this.textFields[index])) {
        this.textFields[index] = this.textFields[index].slice(0, -1);
      }
    },
    goToNextPage(): void {
      const queryString = this.textFields.map(encodeURIComponent).join(',');
      this.$router.push({ name: 'NextPage', query: { strings: queryString } });
    }
  }
});
</script>

<style>
.field {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}

button {
  margin-left: 8px;
}
</style>
