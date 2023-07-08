<template>
  <div id="app">
    <div class="center section">
      <img
        src="https://a-us.storyblok.com/f/1001647/x/a479bc0901/conveyour-logo.svg"
        alt="ConveYour Logo"
      />
      <h1 class="header">Pricing Calculator</h1>

      <h2>Monthly Active Learners</h2>
      <div style="line-height: 24px">
        <input type="number" v-model="malCount" />
        <input
          type="range"
          v-model="malCount"
          min="100"
          max="20000"
          style="vertical-align: middle"
        />
      </div>
    </div>

    <div class="center section">
      <VolumePricingTable
        :unitCount="+malCount"
        :startingPrice="malStartingPrice"
        :stepUnits="malStepUnits"
        :stepDiscount="malStepDiscount"
        v-model="malTotal"
      >
      </VolumePricingTable>
    </div>
    <div class="center section">
      <h2>Video Upload Licenses</h2>
      <div style="line-height: 24px">
        <input type="number" v-model="videoUploadCount" />
        <input
          type="range"
          v-model="videoUploadCount"
          min="0"
          max="20000"
          style="vertical-align: middle"
        />
      </div>
    </div>
    <div class="center section">
      <VolumePricingTable
        :unitCount="+videoUploadCount"
        :startingPrice="videoUploadStartingPrice"
        :stepUnits="videoUploadStepUnits"
        :stepDiscount="videoUploadStepDiscount"
        v-model="videoUploadTotal"
      >
      </VolumePricingTable>
    </div>

    <div class="center section">
      <table>
        <thead>
          <th>Item</th>
          <th>Count</th>
          <th>Sub-Total</th>
        </thead>
        <tbody>
          <tr>
            <td>MAL Licenses</td>
            <td>{{ format(+malCount) }}</td>
            <td>${{ format(malTotal) }}</td>
          </tr>
          <tr>
            <td>Video Upload / Coaching Licenses</td>
            <td>{{ format(+videoUploadCount) }}</td>
            <td>${{ format(videoUploadTotal) }}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <th colspan="2"></th>
            <th colspan="1">Total</th>
          </tr>
          <tr>
            <th colspan="2"></th>
            <th>${{ format(malTotal + videoUploadTotal) }}</th>
          </tr>
        </tfoot>
      </table>
    </div>
  </div>
</template>

<script>
import VolumePricingTable from './components/VolumePricingTable.vue';
export default {
  name: 'App',
  components: {
    VolumePricingTable,
  },
  data() {
    const params = Object.fromEntries(
      new URLSearchParams(window.location.search)
    );

    let data = {
      malCount: 0,
      malStartingPrice: 12.0,
      malStepUnits: 2000,
      malStepDiscount: 0.05,

      malTotal: 0.0,

      videoUploadCount: 0,
      videoUploadStartingPrice: 2,
      videoUploadStepUnits: 1000,
      videoUploadStepDiscount: 0.0,

      videoUploadTotal: 0.0,
    };

    for (const key in data) {
      if (typeof params[key] !== 'undefined') {
        data[key] = +params[key];
      }
    }

    return data;
  },
  methods: {
    format(number) {
      return number.toLocaleString('en-US');
    },
  },
};
</script>

<style>
.center {
  text-align: center;
}

.section {
  margin-top: 2vh;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  color: #2c3e50;
  margin-top: 60px;
}

table {
  display: inline-block;
}

th,
td {
  padding: 5px 10px;
  text-align: left;
}

th {
  text-transform: capitalize;
  background: whiteSmoke;
}

tr::nth-of-type(even) td {
  background: whiteSmoke;
}
</style>
