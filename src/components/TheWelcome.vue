<template>
  <div>
    <div class="open-modal-button lp">
      <button @click="openModal">Open Modal</button>
    </div>
    <div v-if="showModal" class="modal">
      <div class="modal-container lp">
        <div class="header-container d-flex ly-spacebetween-start">
          <h4>Supermarket Survey!</h4>
          <div @click="closeModal" class="close"></div>
        </div>

        <div class="ly-even-2 form-container">
          <div class="input-item custom-select">
            <div class="input-name">Select a Fruit</div>
            <div class="selected-option" @click="toggleDropdown">{{ selectedOption }}
              <i class="arrow-icon" :class="{ 'arrow-up': showOptions }"></i>
            </div>
            <div class="options" v-if="showDropdown">
              <div
                  class="option"
                  v-for="option in options"
                  :key="option.value"
                  @click="selectOption(option)"
              >
                {{ option.label }}
              </div>
            </div>
          </div>
          <div class="input-item search-select">
            <div class="input-name">Select a Food Item</div>
            <SelectedFilter search-field-text="Filter options"/>
          </div>
          <div class="input-item count-input">
            <div class="input-name">Favorite Food</div>
            <input v-model="inputText" @input="updateLetterCount" type="text" placeholder="E.g. Pizza">
            <p class="count-item">{{ letterCount }}/80</p>
          </div>
          <div class="input-item number-input">
            <NumberInput v-model="numberValue" format="currency" :maxValue="100" label="Food Budget"/>
          </div>
        </div>
        <div class="action-buttons">
          <button @click="closeModal">Cancel</button>
          <button class="btn-primary" @click="closeModal">Submit</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">

import {defineComponent, ref} from 'vue';
import NumberInput from './NumberInput.vue';
import SelectedFilter from "./SelectedFilter.vue";

export default defineComponent({
  components: {
    SelectedFilter,
    NumberInput,
  },
  data() {
    return {
      numberValue: 0,

      selectedOption: 'Select Option',
      showDropdown: false,
      options: [
        {value: 'option1', label: 'Watermelon'},
        {value: 'option2', label: 'Orange'},
        {value: 'option3', label: 'Banana'},
        {value: 'option3', label: 'Kiwi'}
      ],
      inputText: '',
      letterCount: 0
    };
  },

  name: 'modalComponent',
  setup() {
    const showModal = ref(false);

    const openModal = (): void => {
      showModal.value = true;
    };

    const closeModal = (): void => {
      showModal.value = false;
    };

    return {
      showModal,
      openModal,
      closeModal,
    };
  },
  methods: {
    updateLetterCount() {
      this.inputText = this.inputText.slice(0, 80); // Truncate input to 80 characters
      this.letterCount = this.inputText.length;
    },
    toggleDropdown() {
      this.showDropdown = !this.showDropdown;
    },
    selectOption(option) {
      this.selectedOption = option.label;
      this.showDropdown = false;
    }
  },
  computed: {
    filteredOptions() {
      return this.options.filter(option => option.label.toLowerCase().includes(this.filterText.toLowerCase()));
    }
  }
});

</script>
