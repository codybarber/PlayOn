<template>
  <div>
    <b-navbar class="navbar-style" type="dark" sticky>
      <b-navbar-brand href="#">
        <img src="https://www.nfhsnetwork.com/_nuxt/img/3c95376.svg" height="45" width="30" alt="NFSH Network Logo">
        NFHS Network
      </b-navbar-brand>
    </b-navbar>
    <b-container>
      <b-row class="mt-3 mb-2">
        <b-col xl="12">
          <b-row class="mb-2">
            Select a State Association and Dates below to search events:
          </b-row>
          <b-row>
            <b-col xl="4" align="center">
              <b-form-select
                id="state_key"
                class="mb-2 mr-sm-2 mb-sm-1"
                required
                :options="states"
                v-model="state_association_key"
                :value="null"
                :state="valid_state_selection"
              />
            </b-col>
            <b-col xl="3" align="center">
              <b-form-datepicker 
                class="mb-2 mr-sm-2 mb-sm-1"
                v-model="start_date" 
                placeholder="Start date" 
                locale="en"
              />
            </b-col>
            <b-col xl="3" align="center">
              <b-form-datepicker 
                class="mb-2 mr-sm-2 mb-sm-1"
                v-model="end_date" 
                placeholder="End date" 
                locale="en"
              />
            </b-col>
            <b-col xl="2" align="center">
              <b-button 
                block
                class="mb-2 mr-sm-2 mb-sm-1"
                variant="outline-danger" 
                @click="reset"
              >
                Reset Search
              </b-button>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
      <b-row v-if="events" class="mt-xl-2 mt-sm-2">
        <b-col xl="12">
          <hr class="separator">
          <events :events="events" :loading="busy"/>
        </b-col>
      </b-row>
    </b-container>   
  </div>
</template>

<script>
import moment from 'moment';
import Events from './Events.vue';
export default {
  name: 'Home',
  components: {
    Events
  },
  data () {
    return {
      state_association_key: null,
      start_date: null,
      end_date: null,
      busy: false,
      valid_state_selection: null,
      states: [
        {
          text: 'Please select a state association',
          value: null
        },
        {
          text: 'Georgia High School Association (GHSA)',
          value: '18bad24aaa'
        },
        {
          text: 'Texas State Association (UIL)',
          value: '542bc38f95'
        }
      ],
      events: null
    }
  },
  computed: {
    url () {
      return `https://challenge.nfhsnetwork.com/v2/search/events/upcoming?state_association_key=${this.state_association_key}&card=true&size=50&start=0&from=${this.start_date ? moment(this.start_date).toISOString() : ''}&to=${this.end_date ? moment(this.end_date).toISOString() : ''}`
    }
  },
  watch: {
    url () {
      if (this.state_association_key != null) {
        this.valid_state_selection = true;
        this.submit();
      } else {
        this.valid_state_selection = false;
      }  
    }
  },
  methods: {
    submit () {
      this.busy = true;
      fetch(this.url)
        .then(async response => {
          const data = await response.json();
          this.busy = false;
          this.events = data.items;
        })
        .catch(error => {
          this.errorMessage = error;
          this.busy = false;
          console.error("There was an error!", error);
        });
    },
    reset () {
      this.state_association_key = null;
      this.start_date = null;
      this.end_date = null;
      this.events = null;
    }
  }
}
</script>

<style scoped>
.navbar-style {
  background-color: #03223D !important;
  height: 50px;
}

.separator {
  border: 1px solid white;
  width: 75%;
  opacity: 0.3;
}
</style>
