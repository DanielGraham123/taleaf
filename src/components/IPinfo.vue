<template>
  <div
    class="absolute -bottom-32 md:-bottom-20 bg-gray-300 rounded-md flex-col md:flex-row px-8 py-8 pb-12 gap-y-4 gap-x-12 grid grid-cols-2 md:grid-cols-4 shadow-lg md:w-5/6"
  >
    <!-- Flex Item 1 -->
    <div class="flex flex-col text-left md:text-center">
      <h3 class="text-xs uppercase">IP Address</h3>
      <span class="text-sm md:text-lg lg:text-2xl">{{ ipData.address }}</span>
    </div>
    <!-- Flex Item 2 -->
    <div class="flex flex-col text-left md:text-center">
      <h3 class="text-xs uppercase">Location</h3>
      <span class="text-sm md:text-lg lg:text-2xl">{{ ipData.state }}</span>
    </div>
    <!-- Flex Item 3 -->
    <div class="flex flex-col text-left md:text-center">
      <h3 class="text-xs uppercase">Timezone</h3>
      <span class="text-sm md:text-lg lg:text-2xl"
        >GMT{{ ipData.timezone }}</span
      >
    </div>
    <!-- Flex Item 4 -->
    <div class="flex flex-col text-left md:text-center">
      <h3 class="text-xs uppercase">ISP</h3>
      <span
        class="text-sm md:text-lg lg:text-2xl truncate"
        data-bs-toggle="tooltip"
        :title="ipData.isp"
        >{{ ipData.isp }}</span
      >
    </div>
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  props: ["ipData"],

  data() {
    return {};
  },
  mounted() {
    console.log("ip Data", this.ipData);
  },
});
</script>
