<template>
  <div class="background">
    <h1 class="countdown">{{ remaining }}</h1>
  </div>
</template>

<style lang="scss" scoped>
.background {
  width: 100vw;
  height: 100vh;
  background: url("../assets/globe.svg");
  background-repeat: no-repeat;
  background-size: 80% 80%;
  background-position: center;

  display: grid;
  place-items: center;

  .countdown {
    font-size: 200px;
    text-shadow: 8px 8px 30px #000000;
  }
}
</style>

<script lang="ts">
import { defineComponent } from "vue";
import * as io from "socket.io-client";
import dayjs from "@/utils/day";
import config from "@/config/config";

export default defineComponent({
  name: "Home",
  components: {},
  data() {
    return {
      remaining: null as any,
    };
  },
  methods: {
    getRemaining() {
      const socket = io(config.wss.url);

      socket.on("connect", () => {
        console.log("wss connected");

        socket.on("remaining", ({ data }: any) => {
          console.log(dayjs.duration(data));
          this.remaining = new Date(data * 1000).toISOString().substr(11, 8); //format seconds to hh:mm:ss string
        });
      });
    },
  },
  created() {
    this.getRemaining();
  },
});
</script>
