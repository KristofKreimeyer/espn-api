<template>
  <div id="app">

  </div>
</template>

<script>

import cheerio from 'cheerio';
import request from "request";
export default {
  name: 'App',
  data () {
    return {
      nba: {}
    }
  },
  methods: {
    fetchNBAData() {
      request('https://www.espn.com/nba/scoreboard', (error,response,html) => {
        if(!error && response.statusCode === 200) {
          const $ = cheerio.load(html);
          const scripts = $("script").toArray();
          const scoreboard = scripts.find(script =>
              script.children[0] &&
              script.children[0].data.includes("window.espn.scoreboardData"))
              .children[0].data;

          const strippedData = scoreboard
              .replace("window.espn.scoreboardData", "")
              .replace("=", "")
              .replace("if(!window.espn_ui.device.isMobile){window.espn.loadType = \"ready\"};", "")
              .replace(/;/g, "").split("window.espn.scoreboardSettings")[0].trim();

          const data = JSON.parse(strippedData);

          this.nba = data;
          console.log(this.nba);
        }
      })
    }
  },
  mounted() {
    this.fetchNBAData();
  }
}
</script>

<style>

</style>
