<template>
  <table>
    <tr>
      <th>Misson Name</th>
      <th>Rocket Name</th>
      <th>Rocket Type</th>
      <th>Launch Date</th>
    </tr>
    <tr v-for="launch in launches" :key="launch.mission_name">
      <td>{{ launch.mission_name }}</td>
      <td>{{ launch.rocket.rocket_name }}</td>
      <td>{{ launch.rocket.rocket_type }}</td>
      <td>{{ launch.launch_date_local }}</td>
    </tr>
  </table>
</template>

<script>
export default {
  data() {
    return {
      launches: [],
    };
  },
  created() {
    this.fetchLaunches();
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
          this.launches = data.data.launches;
          console.log(this.launches);
        });
    },
  },
};
</script>
