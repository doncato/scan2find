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
    <div class="field checkbox-wrapper">
      <input
        v-model="case"
        type="checkbox"
        id="case"
        value="case"
        class="checkbox"
      />
      <label for="case">Case Sensitive?</label>
    </div>
    <div class="field checkbox-wrapper">
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
      case: false as boolean,
      sticky: false as boolean
    };
  },
  computed: {
    isFormValid(): boolean {
      return this.textFields.every(field => /^[a-zA-Z0-9 \"\:\,\-\{\}\(\)]*$/.test(field));
    }
  },
  methods: {
    addField(): void {
      this.textFields.push('');
      this.$nextTick(() => {
        const inputRefs = this.$refs.inputFields as HTMLInputElement[];
        inputRefs[inputRefs.length - 1]?.focus();
      });
    },
    delField(i: number): void {
      this.textFields.splice(i, 1);
    },
    validateInput(index: number): void {
      if (!/^[a-zA-Z0-9 \"\:\,\-\{\}\(\)]*$/.test(this.textFields[index] ?? "")) {
        this.textFields[index] = this.textFields[index]?.slice(0, -1) ?? "";
      }
    },
    goToFindPage(): void {
      const queryString = this.textFields.map(encodeURIComponent).join(';');
      this.$router.push({ name: 'Find', query: { strings: queryString, case: this.case.toString(), sticky: this.sticky.toString() } });
    }
  }
});
</script>

<style>
  .checkbox-wrapper .checkbox {
    appearance: none;
    background-color: var(--color-background-soft);
    border-radius: 72px;
    border-style: none;
    flex-shrink: 0;
    height: 20px;
    margin: 0;
    position: relative;
    width: 30px;
    margin-right: 1em;
  }

  .checkbox-wrapper .checkbox::before {
    bottom: -6px;
    content: "";
    left: -6px;
    position: absolute;
    right: -6px;
    top: -6px;
  }

  .checkbox-wrapper .checkbox,
  .checkbox-wrapper .checkbox::after {
    transition: all 100ms ease-out;
  }

  .checkbox-wrapper .checkbox::after {
    background-color: var(--color-secondary);
    border-radius: 50%;
    content: "";
    height: 14px;
    left: 3px;
    position: absolute;
    top: 3px;
    width: 14px;
  }

  .checkbox-wrapper input[type=checkbox] {
    cursor: default;
  }

  .checkbox-wrapper .checkbox:hover {
    background-color: var(--color-background-soft);
    transition-duration: 0s;
  }

  .checkbox-wrapper .checkbox:checked {
    background-color: var(--color-primary);
  }

  .checkbox-wrapper .checkbox:checked::after {
    background-color: var(--color-secondary);
    left: 13px;
  }

  .checkbox-wrapper :focus:not(.focus-visible) {
    box-shadow: 0 0 10px rgba(0, 123, 255, 0.5);
    outline: 0;
  }

  .checkbox-wrapper .checkbox:checked:hover {
    background-color: var(--color-primary);
  }
</style>

