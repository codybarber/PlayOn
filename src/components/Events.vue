<template>
  <b-container>
    <b-row>
      <b-col lg="4" offset-lg="8" md="12">
        <b-input-group 
          size="sm" 
          prepend-html="<span>&#128270;</span>"
          class="mb-2"
        >
          <b-form-input
            id="filter-input"
            v-model="filter"
            type="search"
            placeholder="Filter Results..."
          />
        </b-input-group>
      </b-col>
    </b-row>
    <b-row>
      <b-table 
        striped 
        hover
        dark
        :sticky-header="`500px`"
        responsive
        :busy="loading"
        :filter="filter" 
        :items="events" 
        :fields="fields"
      >
        <template #cell(date)="data">
          <span v-html="parseDate(data.value)"></span>
        </template>

        <template #cell(site_url)="data">
          <b-link target="_blank" :href="data.value">Watch Event</b-link>
        </template>
      </b-table>
    </b-row>
  </b-container>
</template>

<script>
import moment from 'moment';
export default {
  name: 'Event',
  data() {
    return {
      filter: null,
      fields: [
        {
          key: 'key',
          label: 'Key',
          sortable: true
        },
        {
          key: 'activity_or_sport',
          label: 'Sport',
          sortable: true
        },
        {
          key: 'headline',
          label: 'Headline',
          sortable: true
        },
        {
          key: 'subheadline',
          label: 'SubHeadline',
          sortable: true
        },
        {
          key: 'date',
          label: 'Start Time',
          sortable: true
        },
        {
          key: 'site_url',
          label: 'Link',
          sortable: false
        }
      ],
    }
  },
  props: {
    events: {
      type: Array,
      required: true
    },
    loading: {
      type: Boolean,
      required: true
    }
  },
  methods: {
    parseDate (date) {
      let day = moment(date).format('L');
      let time = moment(date).format('LT');
      return `${day}<br>${time}`
    }
  }
}
</script>

<style scoped>
.event-card {
  margin: 10px;
}
</style>
