<script lang="ts" setup>
import list from '../assets/list.json'

const props = defineProps<{
  class?: string
  article: string | null
}>()

const emit = defineEmits<{
  reset: []
}>()

const name = computed(() => 
  list.find(l => l.article.toLocaleLowerCase() === props.article?.toLocaleLowerCase())?.name
)

</script>

<template>
  <div>
    <ClientOnly>
    <v-card :class="class">
      <v-card-title v-if="article" class="text-h4">Article trouvé !</v-card-title>
      <v-card-title v-else class="text-h4">Oups!</v-card-title>
      <v-card-subtitle v-if="article">
        <p>{{ article }}</p>
        <p v-if="name" class="font-weight-bold text-h6">{{ name }}</p>
      </v-card-subtitle>
      <v-card-subtitle v-else>
        <p>Une erreur s'est produite, veuillez ressayer</p>
      </v-card-subtitle>
      <v-card-item>
        <div class="d-flex justify-center">
          <div v-if="article">
            x
          </div>
          <v-icon v-else size="128" icon="mdi-alert-circle" color="red"></v-icon>
        </div>
      </v-card-item>
      <v-card-actions>
        <v-btn class="reset" size="x-large" color="info" variant="flat" @click="emit('reset')">RECOMMENCER</v-btn>
      </v-card-actions>
    </v-card>
  </ClientOnly>
  </div>
</template>
<style lang="css" scoped>
.reset {
  width: 100%;
}
</style>