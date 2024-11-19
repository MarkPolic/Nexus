<template>
  <div class="drop_wrap">
    <label :for="id">{{ label }}</label>
    <select
      :id="id"
      v-model="selectedValue"
      @change="handleChange"
      :disabled="isDisabled"
    >
      <option value="" disabled>{{ placeholder }}</option>
      <option v-for="item in options" :key="item" :value="item">
        {{ item }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "VehicleDropdown",
  props: {
    label: {
      type: String,
      required: true,
    },
    id: {
      type: String,
      required: true,
    },
    options: {
      type: Array,
      required: true,
    },
    placeholder: {
      type: String,
      default: "Please select an option",
    },
    isDisabled: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      selectedValue: "",
    };
  },
  methods: {
    handleChange() {
      this.$emit("update:selected", this.selectedValue);
    },
  },
};
</script>

<style lang="scss">
$breakpoint-small: 600px;

label {
  font-size: 16px;
  color: #555;
  font-weight: 500;
  text-align: left;
  margin-bottom: 5px;
  letter-spacing: 0.5px;
  font-family: monospace;
}

select {
  width: 100%;
  height: 50px;
  padding: 0px 3;
  font-size: 16px;
  color: #333;
  background-color: transparent;
  border: none;
  border-radius: 5px;
  outline: none;
  box-sizing: border-box;
  transition: all 0.3s ease;
  margin-top: 5px;
}

select:hover {
  border-color: #aaa;
}

select:focus {
  border-color: transparent;
  box-shadow: none;
}

option {
  font-size: 16px;
  font-family: monospace;
}

option:disabled {
  color: #888;
  background-color: #f9f9f9;
}
option:hover {
  color: black;
  background-color: #aaa;
}

.drop_wrap:hover {
  background: gainsboro;
  border-radius: 50px;
}

.drop_wrap {
  padding: 10px 30px;
}
select::-webkit-scrollbar {
  width: 4px;
}

select::-webkit-scrollbar-thumb {
  background: gray;
  border-radius: 10px;
}
</style>
