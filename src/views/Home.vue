<template>
  <div class="home">
    <AppPlayer />
  </div>
</template>

<script>
// @ is an alias to /src
import AppPlayer from "@/components/AppPlayer.vue";

export default {
  name: "home",
  components: {
    AppPlayer
  }
};
</script>
