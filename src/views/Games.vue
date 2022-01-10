<template>
  <div class="game">
    <game/>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import game from "@/components/JumpRun.vue";

export default defineComponent({
  components: {
    game,
  },
})
</script>


<style scoped lang="scss">

</style>
