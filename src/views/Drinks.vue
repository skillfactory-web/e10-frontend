<template>
  <div class="drinks">
    <h1>Страница со списком напитков и рейтингами</h1>

    <form @keydown.enter="onAddFormSubmit">
      <label>
        Название напитка:
        <input type="text" v-model="new_name">
      </label>
      <br>
      <label>
        Описание:
        <input type="text" v-model="new_description">
      </label>
      <br>
      <label>
        Личный рейтинг:
        <input type="number" min=0 max=10 v-model="new_rating">
      </label>
    </form>

    <table class="drinks-list">
      <thead>
         <tr>
           <th>Название</th>
           <th>Описание</th>
           <th>Рейтинг</th>
         </tr>
      </thead>
      <tbody>
         <tr v-for="drink in drinks" :key="drink.id">
           <td>{{ drink.name }}</td>
           <td>{{ drink.description }}</td>
           <td>{{ drink.rating }}/10</td>
         </tr>
      </tbody>

    </table>
  </div>
</template>

<style>
.drinks-list {
   border: 1px solid black;
   margin-left: auto;
   margin-right: auto;
}
.drinks-list > td {
   text-align: center;
}
</style>

<script>
import axios from 'axios';

const BASE_API_URL = 'http://localhost:8080/api/v1';

export default {
  name: 'ListDrinks',
  components: {
  },
  methods: {
    getDrinks() {
      const jwt = this.$cookies.get('jwt_token');
      const config = {
        headers: {
          Authorization: `Bearer ${jwt}`,
        },
      };

      axios.get(`${BASE_API_URL}/drinks/`, config).then((response) => {
        this.drinks = response.data;
      });
    },
    onAddFormSubmit(event) {
      event.preventDefault();
      const requestData = {
        name: this.new_name,
        description: this.new_description,
        rating: this.new_rating,
      };
      const csrf = this.$cookies.get('csrftoken');
      const jwt = this.$cookies.get('jwt_token');

      const config = {
        headers: {
          'X-CSRFToken': csrf,
          Authorization: `Bearer ${jwt}`,
        },
      };

      axios.post(`${BASE_API_URL}/drinks/`, requestData, config)
        .then(() => {
          this.getDrinks();
        });
      this.new_name = '';
      this.new_description = '';
      this.new_rating = 0;
    },
  },
  data() {
    return {
      drinks: {},
      new_name: '',
      new_description: '',
      new_rating: 0,
    };
  },
  mounted() {
    this.getDrinks();
  },
};
</script>
