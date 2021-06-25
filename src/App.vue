<template>
  <div class="container" id="app">
    <h2>The issues on Github</h2>
    <div class="row">
      <div class="col-sm-8 offset-sm-2 col-md-6 offset-md-3 col-lg-4 offset-lg-4">
        <form action="#" @submit.prevent="getIssues">
          <div class="form-group">
            <input
              type="text"
              placeholder="Owner/Repository Name"
              v-model="repository"
              class="form-control"
            >
          </div>
        </form>

        <div class="alert alert-info" v-show="loading">Loading...</div>
        <div class="alert alert-danger" v-show="errored">An error occured</div>
      </div>
    </div>

    <div class="row" v-show="!loading && issues.length">
      <div class="col-md-12 text-center">
        <BarChart :issues="issues"></BarChart>
      </div>
      <div class="col-md-12 text-center mt-4">
        <SunBurstChart :issues="issues"></SunBurstChart>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import axios from 'axios'

import BarChart from './components/BarChart.vue'
import SunBurstChart from './components/SunBurstChart.vue'

export default {
  name: 'App',
  components: {
    BarChart,
    SunBurstChart
  },
  data() {
    return {
      loading: false,
      errored: false,
      issues: [],
      repository: '',
      startDate: null
    }
  },
  methods: {
    getDateRange() {
      const startDate = moment().subtract(6, 'days')
      const endDate = moment()
      const dates = []

      while (startDate.isSameOrBefore(endDate)) {
        dates.push({
          day: startDate.format('MMM Do YY'),
          issues: 0
        })

        startDate.add(1, 'days')
      }

      return dates
    },
    getIssues() {
      this.loading = true
      this.errored = false
      this.startDate = moment().subtract(6, 'days').format('YYYY-MM-DD')

      axios
        .get(
          `https://api.github.com/search/issues?q=${this.repository}+is:issue+is:open+created:>=${this.startDate}`,
          { params: { per_page: 100 } }
        )
        .then(response => {
          const payload = this.getDateRange()

          response.data.items.forEach(item => {
            const key = moment(item.created_at).format('MMM Do YY')
            const obj = payload.filter(o => o.day === key)[0]
            obj.issues += 1
          })

          this.issues = payload
        })
        .catch(error => {
          console.log(error)
          this.errored = true
        })
        .finally(() => (this.loading = false))
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.bar {
  fill: #319bbe;
}
</style>
