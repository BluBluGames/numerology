<template>
  <div>
    <form @submit.prevent="calculateVibration">
      <div>
        <label for="birth-date">Data urodzenia:</label>
        <input 
          type="date" 
          id="birth-date" 
          v-model="birthDate" 
          required
        />
      </div>

      <div>
        <label for="full-name">Pełne imię i nazwisko:</label>
        <input 
          type="text" 
          id="full-name" 
          v-model="fullName" 
          placeholder="Wprowadź pełne imię i nazwisko"
          required
        />
      </div>

      <div>
        <label for="signature">Podpis:</label>
        <input 
          type="text" 
          id="signature" 
          v-model="signature" 
          placeholder="Wprowadź podpis"
          required
        />
      </div>

      <button type="submit">Oblicz Wibrację</button>
    </form>

    <div v-if="vibrationResult !== null">
      <h3>Wynik:</h3>
      <p>
        Wibracja Urodzenia: {{ vibrationResult }}
        <span v-if="isMasterNumber(vibrationResult)" >(Liczba Mistrzowska)</span>
      </p>
      <p>
        Wibracja Dnia Urodzenia: {{ dayVibration }}
      </p>
      <p>
        Wibracja Wnętrza (samogłoski): {{ innerVibration }}
      </p>
      <p>
        Wibracja Zewnętrzna (spółgłoski): {{ outerVibration }}
      </p>
      <p>
        Wibracja Celów Życiowych: {{ lifeGoalsVibration }}
      </p>
      <p>
        Wibracja Indywidualności: {{ individualityVibration }}
      </p>
      <p>
        Wibracja Temperamentu i Uzdolnień:
        <ul>
          <li>Mentalne: {{ temperamentVibration.mental }}</li>
          <li>Emocjonalne: {{ temperamentVibration.emotional }}</li>
          <li>Intuicyjne: {{ temperamentVibration.intuitive }}</li>
          <li>Artystyczne: {{ temperamentVibration.artistic }}</li>
          <li>Naukowe: {{ temperamentVibration.scientific }}</li>
          <li>Związane z prowadzeniem interesów: {{ temperamentVibration.business }}</li>
          <li>Fizyczne: {{ temperamentVibration.physical }}</li>
        </ul>
      </p>
      <p>
        Wibracja Podpisu: {{ signatureVibration }}
      </p>
      <p>
        Potencjalne Przeznaczenie: {{ potentialDestiny }}
      </p>
      <p>
        Przeznaczenie: {{ destiny }}
      </p>
    </div>
  </div>
</template>

<script>
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
    calculateVibration() {
      if (!this.birthDate || !this.fullName || !this.signature) return;

      // Zamiana daty na cyfry i sumowanie ich
      const numbers = this.birthDate.replaceAll("-", "").split("").map(Number);
      let sum = numbers.reduce((acc, num) => acc + num, 0);

      // Redukcja do jednej cyfry lub liczby mistrzowskiej
      while (sum > 9 && !this.isMasterNumber(sum)) {
        sum = sum
          .toString()
          .split("")
          .reduce((acc, num) => acc + parseInt(num), 0);
      }

      this.vibrationResult = sum;

      // Pobranie dnia urodzenia jako wibracji dnia
      const day = parseInt(this.birthDate.split("-")[2]);
      this.dayVibration = day;

      // Obliczanie wibracji imienia
      const numerologyMap = {
        A: 1, Ą: 1, B: 2, C: 3, Ć: 3, D: 4, E: 5, Ę: 5, F: 6, G: 7, H: 8, I: 9, J: 1,
        K: 2, L: 3, Ł: 3, M: 4, N: 5, Ń: 5, O: 6, Ó: 6, P: 7, R: 9, S: 1, Ś: 1, T: 2,
        U: 3, W: 5, Y: 7, Z: 8, Ź: 8, Ż: 8, V: 4, X: 6, Q: 8
      };
      const letters = this.fullName.replace(/\s+/g, "").toUpperCase().split("");

      const innerSum = letters
        .filter((letter) => "AEIOUYĄĘÓ".includes(letter))
        .map((letter) => numerologyMap[letter] || 0)
        .reduce((acc, num) => acc + num, 0);

      const outerSum = letters
        .filter((letter) => !"AEIOUYĄĘÓ".includes(letter))
        .map((letter) => numerologyMap[letter] || 0)
        .reduce((acc, num) => acc + num, 0);

      this.innerVibration = this.reduceToSingleDigit(innerSum);
      this.outerVibration = this.reduceToSingleDigit(outerSum);

      // Wibracja Celów Życiowych
      this.lifeGoalsVibration = this.reduceToSingleDigit(innerSum + outerSum);

      // Wibracja Indywidualności
      const totalSum = letters
        .map((letter) => numerologyMap[letter] || 0)
        .reduce((acc, num) => acc + num, 0);
      this.individualityVibration = this.reduceToSingleDigit(totalSum);

      // Wibracja Temperamentu i Uzdolnień
      const frequency = letters
        .map((letter) => numerologyMap[letter] || 0)
        .reduce((acc, num) => {
          acc[num] = (acc[num] || 0) + 1;
          return acc;
        }, {});

      this.temperamentVibration.mental = (frequency[1] || 0) + (frequency[7] || 0) + (frequency[8] || 0) + (frequency[9] || 0);
      this.temperamentVibration.emotional = (frequency[2] || 0) + (frequency[3] || 0) + (frequency[6] || 0) + (frequency[9] || 0);
      this.temperamentVibration.intuitive = (frequency[2] || 0) + (frequency[7] || 0) + (frequency[9] || 0);
      this.temperamentVibration.artistic = (frequency[3] || 0) + (frequency[6] || 0) + (frequency[9] || 0);
      this.temperamentVibration.scientific = (frequency[1] || 0) + (frequency[4] || 0) + (frequency[5] || 0) + (frequency[7] || 0) + (frequency[8] || 0) + (frequency[9] || 0);
      this.temperamentVibration.business = (frequency[2] || 0) + (frequency[4] || 0) + (frequency[8] || 0);
      this.temperamentVibration.physical = (frequency[4] || 0) + (frequency[5] || 0) + (frequency[8] || 0);

      // Wibracja Podpisu
      const signatureLetters = this.signature.replace(/\s+/g, "").toUpperCase().split("");
      const signatureSum = signatureLetters
        .map((letter) => numerologyMap[letter] || 0)
        .reduce((acc, num) => acc + num, 0);
      this.signatureVibration = this.reduceToSingleDigit(signatureSum);

      // Potencjalne przeznaczenie
      this.potentialDestiny = this.vibrationResult;

      // Przeznaczenie
      this.destiny = this.reduceToSingleDigit(this.vibrationResult + this.signatureVibration);
    },
    isMasterNumber(number) {
      return [11, 22, 33, 44].includes(number);
    },
    reduceToSingleDigit(number) {
      while (number > 9 && !this.isMasterNumber(number)) {
        number = number
          .toString()
          .split("")
          .reduce((acc, num) => acc + parseInt(num), 0);
      }
      return number;
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
