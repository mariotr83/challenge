<template>
  <div>
    <div class="select-input" @click="toggleOptions">
      <input type="text" :value="selectedOptionLabel" readonly />
      <i class="arrow-icon" :class="{ 'arrow-up': showOptions }"></i>
    </div>
    <div v-if="showOptions" class="options-container">
      <div class="search-container">
        <input type="text" v-model="optionSearchText" placeholder="Search options" ref="optionSearch" />
      </div>
      <select multiple v-model="selectedValues" @blur="hideOptions" @change="onOptionSelected" class="search-select-options">
        <optgroup v-for="(groupOptions, group) in filteredOptions" :label="group" :key="group" class="name-group">
          <option class="option-item"
              v-for="option in filteredGroupOptions(groupOptions, optionSearchText)"
              :value="option.value"
              :key="option.value"
          >
            {{ option.label }}
          </option>
        </optgroup>
      </select>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    searchFieldText: {
      type: String,
      default: 'Search options',
    },
  },
  data() {
    return {
      options: [
        { value: 'watermelon', label: 'Watermelon', group: 'Fruits' },
        { value: 'orange', label: 'Orange', group: 'Fruits' },
        { value: 'lettuce', label: 'Lettuce', group: 'Vegetables' },
        { value: 'tomato', label: 'Tomato', group: 'Vegetables' },
      ],
      searchText: '',
      selectedValues: [],
      showOptions: false,
      optionSearchText: '',
    };
  },
  computed: {
    filteredOptions() {
      const searchText = this.searchText.toLowerCase();
      return this.options.reduce((filtered, option) => {
        if (
            option.label.toLowerCase().includes(searchText) ||
            option.group.toLowerCase().includes(searchText)
        ) {
          if (!filtered[option.group]) {
            filtered[option.group] = [];
          }
          filtered[option.group].push(option);
        }
        return filtered;
      }, {});
    },
    selectedOptionLabel() {
      if (this.selectedValues.length === 0) {
        return 'Select one';
      }
      const selectedOption = this.options.find(option => option.value === this.selectedValues[0]);
      return selectedOption ? selectedOption.label : '';
    },
  },
  methods: {
    toggleOptions() {
      this.showOptions = !this.showOptions;
      if (this.showOptions) {
        this.$nextTick(() => {
          this.$refs.optionSearch.focus();
        });
      }
    },
    hideOptions() {
      this.showOptions = false;
    },
    filteredGroupOptions(groupOptions, searchText) {
      const search = searchText.toLowerCase();
      return groupOptions.filter(option =>
          option.label.toLowerCase().includes(search) ||
          option.group.toLowerCase().includes(search)
      );
    },
    onOptionSelected() {
      this.showOptions = false;
    },
  },
};
</script>

<style>
.select-input {
  position: relative;
}

.input-text {
  width: 100%;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
}

.arrow-icon {
  position: absolute;
  top: 50%;
  right: 10px;
  transform: translateY(-50%);
  border-style: solid;
  border-width: 6px 6px 0 6px;
  border-color: #888 transparent transparent transparent;
  transition: transform 0.3s ease-in-out;
}

.arrow-up {
  transform: translateY(-50%) rotate(180deg);
}

.options-container {
  position: absolute;
  width: 200px;
  z-index: 1;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.search-container {
  padding: 10px;
}

.search-select-options {
  height: 100px !important;
  background: transparent !important;
}

.name-group {
  margin-bottom: 10px;
  color: lightgray;
  font-weight: bold;
  text-transform: uppercase;
}

.option-item {
  color: black;
}

option {
  text-transform: capitalize;
}

</style>
