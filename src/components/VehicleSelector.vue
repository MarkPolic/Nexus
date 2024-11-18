<template>
  <div class="page_wrap">
    <h1>Vehicle Selector</h1>
    <div class="search_dropdown_wrap">
      <div class="select_wap">
        <!-- Year Dropdown -->
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

        <!-- Make Dropdown -->
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

        <!-- Model Dropdown -->
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

        <!-- Search Button -->
      </div>
      <div class="search_wrap">
        <button
          @click="populateSelectedModel"
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

    <!-- Display Selected Vehicle Data After Search -->
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
import VehicleDropdown from "./VehicleDropdown.vue"; // Import the new dropdown component

export default {
  name: "VehicleSelector",
  components: {
    VehicleDropdown, // Register the component
  },
  data() {
    return {
      years: [],
      makes: [],
      models: [],
      modelNames: [],
      selectedYear: "",
      selectedMake: "",
      selectedModelName: "", // Store only the selected model name here until the search button is clicked
      selectedModel: null, // Will store the full selected model object after search
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
        this.years = response.data.map((item) => item.year); // Extract only the year values
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
        this.makes = response.data.map((item) => item.make) || []; // Extract makes
        this.models = []; // Clear models when year changes
        this.selectedMake = ""; // Clear selected make when year changes
        this.selectedModelName = ""; // Clear selected model name when year changes
        this.selectedModel = null; // Clear selected model when year changes
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
        this.models = response.data || []; // Assuming response is an array of model objects
        this.modelNames = this.models.map((model) => model.model); // Update model names list for dropdown
      } catch (error) {
        console.error("Error fetching models:", error);
      }
    },
    onYearSelected(year) {
      this.selectedYear = year;
      this.fetchMakes(); // Fetch makes after selecting a year
    },
    onMakeSelected(make) {
      this.selectedMake = make;
      this.fetchModels(); // Fetch models after selecting a make
    },
    onModelSelected(model) {
      this.selectedModelName = model; // Only store the model name for now
    },
    populateSelectedModel() {
      // This function is triggered by the search button click
      if (!this.selectedYear || !this.selectedMake || !this.selectedModelName)
        return;

      // Find the full model object based on the selected model name
      const model = this.models.find((m) => m.model === this.selectedModelName);
      if (model) {
        this.selectedModel = model; // Store the full selected model data
      }
    },
  },
  mounted() {
    this.fetchYears(); // Fetch years when component is mounted
  },
};
</script>

<style scoped lang="scss">
/* Add your styles for the VehicleSelector and vehicle card here. */
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

/* Styling for each select element in a row */
.select_group {
  display: flex;
  flex-direction: column;
  flex: 1;
}

/* Styling for labels */
label {
  font-size: 16px;
  color: #555;
  font-weight: 500;
  text-align: left;
  margin-bottom: 5px;
  letter-spacing: 0.5px;
  font-family: monospace;
}

/* Styling for the selects */
select {
  width: 100%;
  height: 50px;
  font-size: 16px;
  color: #333;
  background-color: transparent;
  border: 1px solid #ddd;
  border-radius: 5px;
  box-sizing: border-box;
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
  margin: 20px auto; /* Center the card */

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
    border-collapse: collapse; /* Collapse borders for a clean table look */
    margin-top: 20px;
  }

  th,
  td {
    padding: 12px;
    text-align: left;
    font-size: 16px;
    font-family: monospace;
    border-bottom: 1px solid #f0f0f0; /* Add subtle border between rows */
  }

  th {
    background-color: #f4f4f4; /* Background color for table headers */
    font-weight: bold;
  }

  td {
    background-color: #ebebeb; /* Background color for data cells */
  }

  tr:hover {
    background-color: #f9f9f9; /* Light hover effect for rows */
  }
}

/* Optional: Add subtle hover effect on the vehicle card */
</style>
