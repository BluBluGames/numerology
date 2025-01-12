<template>
  <div>
    <form @submit.prevent="calculateVibration">
      <div>
        <label for="birth-date">Date of Birth:</label>
        <input 
          type="date" 
          id="birth-date" 
          v-model="birthDate" 
          required
        />
      </div>

      <div>
        <label for="full-name">Full Name:</label>
        <input 
          type="text" 
          id="full-name" 
          v-model="fullName" 
          placeholder="Enter your full name"
          required
        />
      </div>

      <div>
        <label for="signature">Signature:</label>
        <input 
          type="text" 
          id="signature" 
          v-model="signature" 
          placeholder="Enter your signature"
          required
        />
      </div>

      <button type="submit">Calculate Vibration</button>
    </form>

    <div v-if="vibrationResult !== null">
      <h3>Result:</h3>
      <p>
        Life Path Number: {{ vibrationResult }}
        <span v-if="isMasterNumber(vibrationResult)"> (Master Number)</span>
      </p>
      <p>Day Vibration: {{ dayVibration }}</p>
      <p>Inner Vibration (Vowels): {{ innerVibration }}</p>
      <p>Outer Vibration (Consonants): {{ outerVibration }}</p>
      <p>Life Goals Vibration: {{ lifeGoalsVibration }}</p>
      <p>Individuality Vibration: {{ individualityVibration }}</p>
      <p>Temperament and Talents:</p>
      <ul>
        <li>Mental: {{ temperamentVibration.mental }}</li>
        <li>Emotional: {{ temperamentVibration.emotional }}</li>
        <li>Intuitive: {{ temperamentVibration.intuitive }}</li>
        <li>Artistic: {{ temperamentVibration.artistic }}</li>
        <li>Scientific: {{ temperamentVibration.scientific }}</li>
        <li>Business: {{ temperamentVibration.business }}</li>
        <li>Physical: {{ temperamentVibration.physical }}</li>
      </ul>
      <p>Signature Vibration: {{ signatureVibration }}</p>
      <p>Potential Destiny: {{ potentialDestiny }}</p>
      <p>Destiny: {{ destiny }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "NumerologyPortrait",
  data() {
    return {
      birthDate: "",
      fullName: "",
      signature: "",
      vibrationResult: null,
      dayVibration: null,
      innerVibration: null,
      outerVibration: null,
      lifeGoalsVibration: null,
      individualityVibration: null,
      temperamentVibration: {
        mental: 0,
        emotional: 0,
        intuitive: 0,
        artistic: 0,
        scientific: 0,
        business: 0,
        physical: 0,
      },
      signatureVibration: null,
      potentialDestiny: null,
      destiny: null,
    };
  },
  methods: {
    async calculateVibration() {
      if (!this.birthDate || !this.fullName || !this.signature) {
        alert("Please fill in all fields.");
        return;
      }

      try {
        const response = await axios.post('http://localhost:5000/api/numerology/calculate', {
          birth_date: this.birthDate,
          full_name: this.fullName,
          signature: this.signature
        });

        const data = response.data;

        this.vibrationResult = data.vibration_result;
        this.dayVibration = data.day_vibration;
        this.innerVibration = data.inner_vibration;
        this.outerVibration = data.outer_vibration;
        this.lifeGoalsVibration = data.life_goals_vibration;
        this.individualityVibration = data.individuality_vibration;
        this.temperamentVibration = data.temperament_vibration;
        this.signatureVibration = data.signature_vibration;
        this.potentialDestiny = data.potential_destiny;
        this.destiny = data.destiny;

      } catch (error) {
        console.error("Error calculating vibration:", error);
        alert("An error occurred while calculating the vibrations. Please try again." + error);
      }
    },

    isMasterNumber(number) {
      return [11, 22, 33, 44].includes(number);
    },
  },
};
</script>

<style scoped>
form {
  margin-bottom: 1rem;
}
label {
  display: block;
  margin-bottom: 0.5rem;
}
input {
  padding: 0.5rem;
  margin-bottom: 1rem;
}
button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
div h3 {
  margin-top: 1rem;
}
p {
  font-size: 1.2rem;
  font-weight: bold;
}
</style>
