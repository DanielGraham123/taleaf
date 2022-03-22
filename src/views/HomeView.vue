<template>
  <div class="flex flex-col h-screen max-h-screen max-w-screen">
    <!-- Search Results -->
    <div
      class="z-20 flex justify-center relative bg-[url('pattern-bg.jpg')] bg-cover px-4 pt-12 pb-28"
    >
      <!-- Search Input -->
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center font-mono text-2xl pb-4">
          IP Address Tracker
        </h1>

        <div class="flex">
          <input
            type="text"
            v-model="queryIp"
            class="flex-1 py-2 px-3 font-sans rounded-tl-lg rounded-bl-lg focus:outline-none"
            placeholder="Search for any IP Address or leave empty to get your IP info"
            @keyup.enter="getIpInfo"
          />
          <i
            class="cursor-pointer bg-gradient-to-r from-purple-500 to-blue-700 text-white px-4 flex items-center rounded-tr-lg rounded-br-lg fa-solid fa-chevron-right hover:bg-gradient-to-l"
            @click="getIpInfo"
          ></i>
        </div>
      </div>

      <!-- IP Info -->
      <IPInfo v-if="ipInfo" :ipData="ipInfo" />
    </div>

    <!-- Map -->
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>

<script>
import IPInfo from "@/components/IPinfo.vue";
import leaflet from "leaflet";
import { onMounted, onBeforeMount, ref } from "vue";
import axios from "axios";

export default {
  name: "HomeView",
  components: { IPInfo },

  setup() {
    let map, clientIp;
    const queryIp = ref("");
    const ipInfo = ref(null);

    const getIpInfo = async () => {
      try {
        const data = await axios.get(
          `https://geo.ipify.org/api/v2/country?apiKey=at_PIOkgrC3d5RgCXvYQ6LJCQ4UELwqg&ipAddress=${queryIp.value}`
        );

        const data_extra = await axios.get(
          `http://api.ipstack.com/${queryIp.value}?access_key=1ea0c8e4ff263e3911e784e0db4856ce`
        );

        const result = data.data;
        result["more"] = data_extra.data;
        console.log("result: ", result);

        ipInfo.value = {
          address: result.ip,
          isp: result.isp,
          state: result.location.region,
          timezone: result.location.timezone,
          lat: result.more.latitude,
          lng: result.more.longitude,
        };
        leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(map);

        map.setView([ipInfo.value.lat, ipInfo.value.lng], 13);
      } catch (err) {
        alert(err.message);
      }
    };

    onBeforeMount(async () => {
      const ip_data = await axios.get(
        "https://ipgeolocation.abstractapi.com/v1/?api_key=20c476c70f0c4e9a935377a76c5ae398"
      );

      clientIp = ip_data.data;
      queryIp.value = clientIp.ip_address;
      console.log("client ip: ", clientIp.data);

      if (clientIp) {
        console.log("new ip: ", clientIp);
        leaflet.marker([clientIp.latitude, clientIp.latitude]).addTo(map);
        map.setView([clientIp.latitude, clientIp.longitude], 6);
      }
    });

    onMounted(() => {
      map = leaflet
        .map("mapid")
        .setView(
          !clientIp ? [51.505, -0.09] : [clientIp.latitude, clientIp.longitude],
          9
        );

      leaflet
        .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiZGFuaWRldiIsImEiOiJjbDExdXM4bDYwMG16M2ptdmFjMXdhbHlpIn0.xcZOe6I3gmnUyXcVdKJAZQ",
          {
            attribution:
              'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              "pk.eyJ1IjoiZGFuaWRldiIsImEiOiJjbDExdXM4bDYwMG16M2ptdmFjMXdhbHlpIn0.xcZOe6I3gmnUyXcVdKJAZQ",
          }
        )
        .addTo(map);
    });

    return {
      queryIp,
      ipInfo,
      getIpInfo,
    };
  },
};
</script>
