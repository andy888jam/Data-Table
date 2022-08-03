<template>
  <div>
    <div class="searchBar">
      <input placeholder="Search..." @input="onInput" />
    </div>
    <table>
      <tr>
        <th>
          Misson Name
          <button @click="ascendingMission">&#9650;</button>
          <button @click="descendingMission">&#9660;</button>
        </th>
        <th>
          Rocket Name
          <button @click="ascendingRocketName">&#9650;</button>
          <button @click="descendingRocketName">&#9660;</button>
        </th>
        <th>
          Rocket Type
          <button @click="ascendingRocketType">&#9650;</button>
          <button @click="descendingRocketType">&#9660;</button>
        </th>
        <th>
          Launch Date
          <button @click="ascendingLaunchDate">&#9650;</button>
          <button @click="descendingLaunchDate">&#9660;</button>
        </th>
      </tr>
      <tr v-for="launch in launches" :key="launch.mission_name">
        <td>{{ launch.mission_name }}</td>
        <td>{{ launch.rocket.rocket_name }}</td>
        <td>{{ launch.rocket.rocket_type }}</td>
        <td>{{ launch.launch_date_local | formatTime }}</td>
      </tr>
    </table>
    <div class="pagination" @click="switchPage">
      <span
        v-for="(item, index) in pages"
        :key="index"
        :class="{ active: currentPage === item }"
        >{{ item }}</span
      >
    </div>
  </div>
</template>

<script>
import moment from 'moment';

const MOVIES_PER_PAGE = 20;

export default {
  data() {
    return {
      allLaunches: [],
      filteredLaunches: [],
      launches: [],
      pages: [],
      currentPage: 1,
    };
  },
  created() {
    this.fetchLaunches();
  },
  filters: {
    formatTime(dateTime) {
      const arr = dateTime.split('T');
      const date = arr[0];
      return moment(date).format('YYYY/MM/DD');
    },
  },
  methods: {
    fetchLaunches() {
      fetch('https://api.spacex.land/graphql/', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          query: `
            query Launches {
              launches {
                mission_name
                rocket {
                  rocket_name
                  rocket_type
                }
                launch_date_local
              }
            }
            `,
        }),
      })
        .then(response => response.json())
        .then(data => {
          this.allLaunches = data.data.launches;
          this.launches = this.getItemByPage(1);
          this.renderPaginator(this.allLaunches.length);
        });
    },
    getItemByPage(page) {
      this.currentPage = page;
      const startIndex = (page - 1) * MOVIES_PER_PAGE;
      let data = this.filteredLaunches.length
        ? this.filteredLaunches
        : this.allLaunches;
      return data.slice(startIndex, startIndex + MOVIES_PER_PAGE);
    },
    renderPaginator(amount) {
      const numberOfPages = Math.ceil(amount / MOVIES_PER_PAGE);
      const pagesArr = [];
      for (let page = 1; page <= numberOfPages; page++) {
        pagesArr.push(page);
      }
      this.pages = pagesArr;
    },
    switchPage(event) {
      this.currentPage = Number(event.target.innerText);
      this.launches = this.getItemByPage(this.currentPage);
    },
    ascendingMission() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.mission_name > b.mission_name ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    descendingMission() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.mission_name < b.mission_name ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    ascendingRocketName() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.rocket.rocket_name > b.rocket.rocket_name ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    descendingRocketName() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.rocket.rocket_name < b.rocket.rocket_name ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    ascendingRocketType() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.rocket.rocket_type > b.rocket.rocket_type ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    descendingRocketType() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.rocket.rocket_type < b.rocket.rocket_type ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    ascendingLaunchDate() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.launch_date_local > b.launch_date_local ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    descendingLaunchDate() {
      if (!this.filteredLaunches.length) {
        return;
      } else {
        this.filteredLaunches = this.filteredLaunches.sort(function (a, b) {
          return a.launch_date_local < b.launch_date_local ? 1 : -1;
        });
      }
      this.launches = this.getItemByPage(1);
    },
    onInput(event) {
      const keyword = event.target.value.trim().toLowerCase();
      this.filteredLaunches = this.allLaunches.filter(
        launch =>
          launch.mission_name.toLowerCase().includes(keyword) ||
          launch.rocket.rocket_name.toLowerCase().includes(keyword) ||
          launch.rocket.rocket_type.toLowerCase().includes(keyword) ||
          this.formatSearchTime(launch.launch_date_local)
            .toLowerCase()
            .includes(keyword)
      );
      if (this.filteredLaunches.length) {
        this.launches = this.getItemByPage(1);
        this.renderPaginator(this.filteredLaunches.length);
        this.currentPage = 1;
      } else {
        this.launches = [];
        this.renderPaginator(this.filteredLaunches.length);
      }
    },
    formatSearchTime(dateTime) {
      const arr = dateTime.split('T');
      const date = arr[0];
      return moment(date).format('YYYY/MM/DD');
    },
  },
};
</script>

<style scoped>
table {
  border-collapse: collapse;
  margin: 10px auto;
}

table,
th,
td {
  border: 1px solid black;
  padding: 10px;
  text-align: center;
}

th,
td {
  width: 200px;
}

.pagination {
  margin-top: 10px;
  display: flex;
  justify-content: center;
  cursor: pointer;
}

.pagination span {
  color: black;
  float: left;
  padding: 8px 16px;
  text-decoration: none;
}

.pagination span.active {
  background-color: dodgerblue;
  color: white;
}

button {
  all: unset;
  margin-left: 15px;
  cursor: pointer;
}

input {
  width: 70%;
  height: 25px;
}

.searchBar {
  margin: 20px;
  text-align: center;
}
</style>
