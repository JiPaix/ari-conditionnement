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
    <v-card :class="props.class">
      <v-card-title>Article trouvé !</v-card-title>
      <v-card-subtitle>
        <p>{{ props.article }}</p>
        <p v-if="name" class="font-weight-bold">{{ name }}</p>
      </v-card-subtitle>
      <v-card-item>
        item
      </v-card-item>
      <v-card-actions>
        <v-btn color="white" variant="flat" @click="emit('reset')">RESET</v-btn>
      </v-card-actions>
    </v-card>
  </ClientOnly>
  </div>

</template>