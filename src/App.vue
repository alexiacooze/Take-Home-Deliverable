<template>
  <div class="app-container">
    <h1>Consent Form</h1>
    <div class="container">
      <h2>Country of Origin</h2>
      <p class="origin">Please Type in Country of Origin</p>
      <input
        type="text"
        v-model="searchText"
        @input="filterCountries"
        @keyup.enter="validateSelection"
        @focus="showDropdown"
        placeholder="Search countries"
        class="search-input"
      />
      <div class="dropdown" v-show="isDropdownVisible">
        <ul class="dropdown-content" v-show="filteredCountries.length > 0">
          <li
            v-for="country in filteredCountries"
            :key="country.name.common"
            @click="selectCountry(country)"
            class="dropdown-item"
          >
            {{ country.name.common }}
          </li>
        </ul>
      </div>
      <div v-if="selectedCountry" class="success-message">
        Selected: {{ selectedCountry.name.common }}
        <span class="checkmark">✔</span>
      </div>
      <div
        v-if="error"
        class="error-message"
        style="color: red; font-weight: bold"
      >
        Error: The entered country is not in the list.
      </div>
    </div>
    <!-- Consent Form -->
    <div class="consent-form">
      <h2>Personal Information Consent</h2>
      <p>
        We Request your Consent to Collect Personal Information for the Purpose
        of This Service.
      </p>
      <label>
        <input type="radio" v-model="consent" value="true"/>
        Yes, I consent to the collection of my personal information.
      </label>
      <label>
        <input type="radio" v-model="consent" value="false" />
        No, I do not consent to the collection of my personal information.
      </label>
    </div>
    <!-- Language Services Form -->
    <div class="language-services-form">
      <h2>Language Services Form</h2>
      <p>
        Do you Require a Call Back from one of our Representatives for Better
        Communication? Please Let us Know.
      </p>
      <label>
        <input type="radio" v-model="languageServices" value=true />
        Yes, I require a Callback Services.
      </label>
      <label>
        <input type="radio" v-model="languageServices" />
        No, I do not Require Callback Services.
      </label>

      <!-- Conditionally render the "Country of Origin" block -->
      <div v-show="languageServices === 'true'">
        <h3>Input Your Callback Number</h3>
        <input
          type="text"
          id="phoneNumberInput"
          placeholder="Enter a phone number"
        />
      </div>
    </div>
    <button class="submit" :disabled="error">Submit</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      searchText: "",
      countries: [],
      isDropdownVisible: false,
      selectedCountry: null,
      error: false,
      consent: null,
      languageServices: null,
      countryOrigin: null,
    };
  },
  computed: {
    // search text string initial input filer
    filteredCountries() {
      const searchTextLowerCase = this.searchText.toLowerCase();
      return this.countries.filter((country) =>
        country.name.common.toLowerCase().includes(searchTextLowerCase)
      );
    },
  },
  methods: {
    // to show dropdown, evaluates against search text string
    filterCountries() {
      this.isDropdownVisible = this.searchText.length > 0;
    },
    showDropdown() {
      this.isDropdownVisible = true;
    },

    // logic for if country is within list, setting error message
    selectCountry(country) {
      if (this.filteredCountries.includes(country)) {
        this.selectedCountry = country;
        this.error = false; 
        // shows the error msg
      } else {
        this.error = true;
        this.selectedCountry = null;
        // removes and resets
      }
      this.isDropdownVisible = false;
    },

    // error message validation
    validateSelection() {
      if (this.searchText && !this.filteredCountries.length) {
        this.error = true;
        this.selectedCountry = null;
      }
    },
    
    // axios call
    fetchCountries() {
      const apiUrl = "https://restcountries.com/v3.1/all";

      axios
        .get(apiUrl)
        .then((response) => {
          this.countries = response.data;
          console.log("API response - countries:", this.countries);
        })
        .catch((error) => {
          console.error("API request failed:", error);
        });
    },
  },
  watch: {
    searchText() {
      this.isDropdownVisible = this.searchText.length > 0;
    },
  },
  mounted() {
    this.fetchCountries();
  },
};
</script>

<style>
.app-container {
  display: flex;
  background-color: rgb(247, 246, 244);
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  margin: 13rem;
}

.origin {
  padding: 3px;
}

.search-input {
  width: 15rem;
  padding: 0.7rem;
}

.consent-form,
.language-services-form,
.container {
  border: 2px solid #ccc;
  padding: 20px;
  margin: 20px;
  text-align: center;
  width: 90%;
}

.success-message {
  color: green;
  font-weight: bold;
  padding: 1rem;
}

.checkmark {
  margin-left: 5px;
}

.dropdown-content {
  display: flex;
  max-height: 150px;
  overflow-y: auto;
  align-content: center;
  flex-direction: column;
  margin: 0rem 20rem;
  text-align: center;
  list-style: none;
  outline: 1.5px solid rgb(179, 46, 46);
  padding-left: 0;
  background-color: rgb(255, 239, 239);
}

.dropdown-item {
  padding: 10px;
  cursor: pointer;
  width: 100%;
  transition: background-color 0.3s;
}

.dropdown-item:hover {
  background-color: rgba(207, 106, 106, 0.2);
}

.submit {
  background-color: rgb(179, 60, 60);
  color: white;
  border: none;
  border-radius: 15px;
  padding: 10px 20px;
  cursor: pointer;
  transition: background-color 0.3s;
  margin-bottom: 1rem;
}

.submit:hover {
  background-color: rgb(112, 6, 6);
}
</style>
