<template>
  <div>
    <label>{{ label }}</label>
    <input
        type="text"
        :value="formattedValue"
        @input="handleInput"
        @blur="handleBlur"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';

interface NumberInputProps {
  value: number;
  format: 'decimal' | 'integer' | 'currency';
  maxValue: number;
  label: string;
}

export default defineComponent({
  props: {
    value: {
      type: Number,
      default: 0,
    },
    format: {
      type: String,
      default: 'decimal',
      validator: (format: string) => ['decimal', 'integer', 'currency'].includes(format),
    },
    maxValue: {
      type: Number,
      default: 999999999,
    },
    label: {
      type: String,
      default: 'Number',
    },
  },
  data() {
    return {
      inputValue: '',
    };
  },
  computed: {
    formattedValue(): string {
      if (this.format === 'decimal') {
        return this.inputValue;
      } else if (this.format === 'integer') {
        return String(Math.floor(Number(this.inputValue)));
      } else if (this.format === 'currency') {
        return Number(this.inputValue).toLocaleString('en-US', {
          style: 'currency',
          currency: 'USD',
        });
      }
      return '';
    },
  },
  watch: {
    value: {
      immediate: true,
      handler(newValue: number) {
        this.inputValue = String(newValue);
      },
    },
  },
  methods: {
    handleInput(event: Event) {
      const inputValue = (event.target as HTMLInputElement).value;
      if (this.format === 'decimal' || this.format === 'integer') {
        this.inputValue = inputValue.replace(/[^0-9.-]/g, '');
      } else if (this.format === 'currency') {
        const sanitizedValue = inputValue.replace(/[^0-9.-]/g, '');
        const parsedValue = parseFloat(sanitizedValue);
        this.inputValue = !isNaN(parsedValue) ? parsedValue.toFixed(2) : '';
      }
    },
    handleBlur() {
      let value = parseFloat(this.inputValue);
      if (isNaN(value)) {
        value = 0;
      } else if (value > this.maxValue) {
        value = this.maxValue;
      }
      this.$emit('update:value', value);
    },
  },
});
</script>
