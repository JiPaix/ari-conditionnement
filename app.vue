<script lang="ts" setup>
  const result = ref<null|string>(null)

  const isDev = computed(() => typeof import.meta.hot !== 'undefined')

</script>


<template>
  <v-app class="bg-grey-darken-3">
    <v-main>
      <v-container class="h-100 d-flex align-center justify-center">
        <BarcodeScanner v-show="result === null" class="bg-grey-darken-4" :dev="isDev" @result="(v) => result ? null : result = v"/>
        <ShowResult v-show="result !== null" class="bg-grey-darken-4" @reset="result = null" :article="result" />
      </v-container>
    </v-main>
  </v-app>
</template>

<style lang="css">
html {
  overflow-y: auto;
}

.h-100 {
  height: 100%;
}


</style>