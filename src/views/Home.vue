<template>
  <div class="home">
    <ViewerContent :page="page"></ViewerContent>
  </div>
</template>

<script>
// @ is an alias to /src
import ViewerContent from "../components/ViewerContent.vue";

export default {
  name: "Home",
  props: {
    page: String,
  },
  data: () => ({}),
  components: {
    ViewerContent,
  }
};
</script>
