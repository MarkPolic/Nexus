<template>
  <div class="page_wrap">
    <h1>Vehicle Selector</h1>
    <div class="search_dropdown_wrap">
      <div class="select_wap">
        <div class="select_group">
          <VehicleDropdown
            label="Year:"
            id="year"
            :options="years"
            placeholder="Select a year"
            @update:selected="onYearSelected"
            :isDisabled="false"
          />
        </div>

        <div class="select_group">
          <VehicleDropdown
            label="Make:"
            id="make"
            :options="makes"
            placeholder="Please select a year first"
            @update:selected="onMakeSelected"
            :isDisabled="!selectedYear"
          />
        </div>

        <div class="select_group">
          <VehicleDropdown
            label="Model:"
            id="model"
            :options="modelNames"
            placeholder="Please select a make first"
            @update:selected="onModelSelected"
            :isDisabled="!selectedMake"
          />
        </div>
      </div>
      <div class="search_wrap" @click="populateSelectedModel">
        <button
          :disabled="!selectedYear || !selectedMake || !selectedModelName"
          class="search-btn"
        >
          <img
            src="@/assets/magnifying-glass.png"
            alt="Button Image"
            class="button-image"
          />
        </button>
      </div>
    </div>

    <div v-if="selectedModel" class="vehicle-card">
      <h2>Vehicle Information</h2>
      <table>
        <thead>
          <tr>
            <th>Field</th>
            <th>Value</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td><strong>Vehicle Type:</strong></td>
            <td>{{ selectedModel.vehicle_type }}</td>
          </tr>
          <tr>
            <td><strong>Year:</strong></td>
            <td>{{ selectedModel.year }}</td>
          </tr>
          <tr>
            <td><strong>Make:</strong></td>
            <td>{{ selectedModel.make }}</td>
          </tr>
          <tr>
            <td><strong>Model:</strong></td>
            <td>{{ selectedModel.model }}</td>
          </tr>
          <tr>
            <td><strong>Original Model:</strong></td>
            <td>{{ selectedModel.original_model }}</td>
          </tr>
          <tr>
            <td><strong>Body Type:</strong></td>
            <td>{{ selectedModel.body_type }}</td>
          </tr>
          <tr>
            <td><strong>Vehicle ID:</strong></td>
            <td>{{ selectedModel.vehicle_id }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import VehicleDropdown from "./VehicleDropdown.vue";

export default {
  name: "VehicleSelector",
  components: {
    VehicleDropdown,
  },
  data() {
    return {
      years: [],
      makes: [],
      models: [],
      modelNames: [],
      selectedYear: "",
      selectedMake: "",
      selectedModelName: "",
      selectedModel: null,
    };
  },
  methods: {
    async fetchYears() {
      try {
        const response = await axios.get(
          "https://rateengine.ship.cars/v2/vehicles/years/?token=5cbe12fb62f4941267d623499a2a4fd5948fd3ef",
          {
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
          }
        );
        this.years = response.data.map((item) => item.year);
      } catch (error) {
        console.error("Error fetching years:", error);
      }
    },
    async fetchMakes() {
      if (!this.selectedYear) return;
      try {
        const response = await axios.get(
          `https://rateengine.ship.cars/v2/vehicles/makes/?year=${this.selectedYear}&token=5cbe12fb62f4941267d623499a2a4fd5948fd3ef`,
          {
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
          }
        );
        this.makes = response.data.map((item) => item.make) || [];
        this.models = [];
        this.selectedMake = "";
        this.selectedModelName = "";
        this.selectedModel = null;
      } catch (error) {
        console.error("Error fetching makes:", error);
      }
    },
    async fetchModels() {
      if (!this.selectedYear || !this.selectedMake) return;
      try {
        const response = await axios.get(
          `https://rateengine.ship.cars/v2/vehicles/models/?year=${this.selectedYear}&make=${this.selectedMake}&token=5cbe12fb62f4941267d623499a2a4fd5948fd3ef`,
          {
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
          }
        );
        this.models = response.data || [];
        this.modelNames = this.models.map((model) => model.model);
      } catch (error) {
        console.error("Error fetching models:", error);
      }
    },
    onYearSelected(year) {
      this.selectedYear = year;
      this.fetchMakes();
    },
    onMakeSelected(make) {
      this.selectedMake = make;
      this.fetchModels();
    },
    onModelSelected(model) {
      this.selectedModelName = model;
    },
    populateSelectedModel() {
      if (!this.selectedYear || !this.selectedMake || !this.selectedModelName)
        return;

      const model = this.models.find((m) => m.model === this.selectedModelName);
      if (model) {
        this.selectedModel = model;
      }
    },
  },
  mounted() {
    this.fetchYears();
  },
};
</script>

<style lang="scss">
$breakpoint-small: 600px;
@media (max-width: $breakpoint-small) {
  .search_wrap {
    width: 100%;
    border-radius: 0px;
    margin: 20px auto;
  }

  .page_wrap {
    width: 100%;
    height: 100%;
    padding: 10px;
  }

  .select_wap {
    flex-direction: column;
  }
  .search_dropdown_wrap {
    flex-direction: column;
  }
  .search_dropdown_wrap .search_wrap {
    width: 100%;
    margin: 20px auto;
    border-radius: 0px;
  }
  .page_wrap .vehicle-card {
    margin: 10px 0px;
    width: max-content;
  }
}
.select_wap {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  width: 100%;
  background-color: #ebebeb;
  padding: 0px;
  border: 1px solid #e0e0e0;
  border-radius: 50px;
  box-sizing: border-box;
}

.select_group {
  display: flex;
  flex-direction: column;
  flex: 1;
}

.search_dropdown_wrap {
  display: flex;
  max-width: 1200px;
  width: 100%;
}
.search_wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0px 20px;
  height: 100%;
  border-radius: 50%;
  background: #ebebeb;
  border: 1px solid #e0e0e0;
  width: 110px;
}
.search_wrap:hover {
  background: gainsboro;

  transition: background-color 0.3s ease;
}

.search-btn {
  background-color: transparent;
  color: white;
  border: none;
  padding: 20px;
  font-size: 16px;
  font-family: monospace;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.button-image {
  width: 30px;
  height: auto;
}
.search-btn:disabled {
  background-color: transparent;
  cursor: not-allowed;
}

.search-btn .magnifying-glass {
  font-size: 18px;
}
.vehicle-card {
  background-color: #ebebeb;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
  max-width: 1200px;
  margin-top: 30px;
  width: 100%;
  font-family: "Roboto", sans-serif;
  color: #333;
  transition: transform 0.3s ease;
  margin: 20px auto;
  h2 {
    font-size: 28px;
    color: #2c3e50;
    font-weight: 700;
    margin-bottom: 20px;
    text-align: center;
    letter-spacing: 1px;
    border-bottom: 2px solid #ccc;
    padding-bottom: 10px;
    font-family: monospace;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }

  th,
  td {
    padding: 12px;
    text-align: left;
    font-size: 16px;
    font-family: monospace;
    border-bottom: 1px solid #f0f0f0;
  }

  th {
    background-color: #f4f4f4;
    font-weight: bold;
  }

  td {
    background-color: #ebebeb;
  }

  tr:hover {
    background-color: #f9f9f9;
  }
}
</style>
