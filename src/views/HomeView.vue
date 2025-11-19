<template>
  <main>
   <div>
    <div v-for="(field, index) in textFields" :key="index" class="field">
      <input
        v-model="textFields[index]"
        @input="validateInput(index)"
        @keydown.enter="addField"
        ref="inputFields"
        type="text"
        placeholder="Enter Search Term"
        :autofocus="index == 0"
      />
      <button v-if="index+1 == textFields.length" @click="addField">+</button>
      <button v-if="index+1 != textFields.length" @click="delField(index)">-</button>
    </div>
    <div class="field">
      <input
        v-model="case"
        type="checkbox"
        id="case"
        value="case"
        class="checkbox"
      />
      <label for="case">Case Sensitive?</label>
    </div>
    <div class="field">
      <input
        v-model="sticky"
        type="checkbox"
        id="sticky"
        value="sticky"
        class="checkbox"
      />
      <label for="sticky">Make result sticky?</label>
    </div>
    <button @click="goToFindPage" :disabled="!isFormValid" class="fullwidth">Go</button>
  </div>
  </main>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  data() {
    return {
      textFields: [''] as string[],
      case: false as bool,
      sticky: false as bool
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
      this.$nextTick(() => {
        const inputRefs = this.$refs.inputFields as HTMLInputElement[];
        console.log(inputRefs);
        inputRefs[inputRefs.length - 1].focus({"focusVisible": true});
      });
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
      this.$router.push({ name: 'Find', query: { strings: queryString, case: this.case, sticky: this.sticky } });
    }
  }
});
</script>
