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
  name: "VehicleDropdown", // Multi-word component name
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

<style scoped>
.select_wap {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  width: 100%;
  max-width: 900px;
  background-color: #ebebeb;
  padding: 0px 20px;
  border: 1px solid #e0e0e0;
  border-radius: 32px;
  box-sizing: border-box;
}

.select_group {
  display: flex;
  flex-direction: column;
  width: 200px;
}

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
  appearance: none;
  outline: none;
  box-sizing: border-box;
  transition: all 0.3s ease;
  margin-top: 5px;
  background-image: url('data:image/svg+xml,%3Csvg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 12 12"%3E%3Cpath fill="none" stroke="%23333" stroke-width="2" d="M3 4l3 3 3-3"%3E%3C/path%3E%3C/svg%3E');
  background-repeat: no-repeat;
  background-position: right 10px center;
  background-size: 12px;
}

select:hover {
  border-color: #aaa;
}

select:focus {
  border-color: transparent;
  box-shadow: none;
}

option {
  padding: 20px;
  font-size: 16px;
  font-family: monospace;
}

option:disabled {
  color: #888;
  background-color: #f9f9f9;
}

#make_drop,
#year_drop {
  border-right: 1px solid #e0e0e0;
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
