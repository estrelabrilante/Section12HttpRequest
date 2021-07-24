<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <!--1) Loading spinner or message if the internet is slow -->
      <p v-if="isLoading">
        Loading..
      </p>
      <!--2) Handling browser side error case -->
      <p v-else-if="!isLoading && error">
        {{ error }}
      </p>
      <!--3) No data stored in database -->
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored datas in array
      </p>
      <!--4) V-else case -->
      <ul v-else-if="!isLoading && results && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  // received results as a prop
  // props: ['results'],
  components: {
    SurveyResult
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null
    };
  },
  methods: {
    //sending request to server to fetch data (from firebase)
    loadExperiences() {
      this.isLoading = true;
      this.error = null;
      fetch(
        'https://vue-http-demo-4198e-default-rtdb.firebaseio.com/surveys.json'
      )
        .then(response => {
          if (response.ok) {
            // JSON method for parsing data in the response
            return response.json();
          }
        })
        //data receives from database
        .then(data => {
          this.isLoading = false;
          console.log(data);
          const results = [];
          for (const id in data) {
            results.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating
            });
          }
          this.results = results;
        })
        .catch(error => {
          console.log(error);
          this.isLoading = false;
          this.error = 'Failed to fetch , please try again';
        });
    }
  },
  //loading components when page is loaded
  mounted() {
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
