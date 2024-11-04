<script lang="ts" setup>
import { StreamBarcodeReader } from '@teckel/vue-barcode-reader'

const props = defineProps<{
  class?: string
  dev?: boolean
}>()

// defineEmits with type safety
const emit = defineEmits<{
  result: [string]
  restart: []
}>()

const loaded = ref(false)

/**
* Check if decoded text is an article
* Ultra-optimized version
*/
function onDecode(result: Record<'text', string>) {
  const str = result.text;
  const len = str.length;
  
  // Fast fail on length
  if(len < 5) return;
  
  // First char check using charCode comparison
  const code0 = str.charCodeAt(0);
  
  // ASCII codes: '0'=48, '2'=50, '6'=54, 'O'=79, 'M'=77
  if(code0 === 48 || code0 === 79) return; // '0' or 'O'
  
  if(code0 === 50 || code0 === 54) { // '2' or '6'
  if(str[1] === '0' && str[2] === '0' && str[3] === '0') return;
} else if(code0 === 77) { // 'M'
if(str[1] === 'A' && str[2] === 'D') {
  // Check last 8 chars for CGA pattern
  for(let i = Math.max(3, len - 8); i <= len - 3; i++) {
    if(str[i] === 'C' && str[i + 1] === 'G' && str[i + 2] === 'A') return;
  }
}
}

emit('result', str);
}
</script>

<template>
  <div>
    <ClientOnly>
      <v-card :class="props.class">
        <v-card-title v-if="loaded" class="text-h4">
          <p>Scan en cours...</p>
          <v-progress-linear
            color="red"
            indeterminate
            height="1"
          />
        </v-card-title>
        <v-card-title v-else class="text-h4">
          <p>Chargement du scanneur</p>
          <v-progress-linear
            color="info"
            indeterminate
            height="1"
          />
        </v-card-title>
        <v-card-subtitle v-if="loaded">
          <p class="d-flex align-self-center">Le scanner peut parfois prendre du temps <v-icon size="small" icon="mdi-sleep" class="zzz mx-1" /></p>
          <p class="d-flex align-self-center font-weight-bold">Retirez la billette ou l'OF de sa pochette plastique pour aller plus vite <v-icon size="small" icon="mdi-lightning-bolt" class="bolt mx-1" /></p>
        </v-card-subtitle>
        <v-card-subtitle v-else>
          <p class="d-flex items-center">Autorisez l'accès à votre caméra si demandé. <v-icon icon="mdi-camera" class="camera mx-1" /></p>
        </v-card-subtitle>
        <v-card-item>
          <div class="d-flex justify-center circular-container" v-if="!loaded">
            <v-progress-circular size="200" width="10" indeterminate></v-progress-circular>
          </div>
          <StreamBarcodeReader
          v-show="loaded"
          :no-front-cameras="true"
          :ms-between-decoding="50"
          @decode="onDecode"
          @loaded="loaded = true"
          />
        </v-card-item>
        <v-card-actions v-if="dev">
          <v-btn color="white" variant="flat" @click="() => onDecode({text: '332A32216799'})">N1</v-btn>
          <v-btn color="white" variant="flat" @click="() => onDecode({text: '332A32216720'})">N6</v-btn>
          <v-btn color="white" variant="flat" @click="() => onDecode({text: 'MAD01CGA3'})">MAD01CGA3</v-btn>
          <v-btn color="white" variant="flat" @click="() => onDecode({text: '001231234'})">001231234</v-btn>
        </v-card-actions>
      </v-card>
    </ClientOnly>
  </div>
</template>
<style lang="css" scoped>
.circular-container {
  padding: 50px;
}
.v-card-subtitle {
  white-space: normal;
}
.v-icon.zzz {
  background: -moz-linear-gradient(top, #2400b2 0%, #2ce7e4 100%);
  background: -webkit-linear-gradient(top, #2400b2 0%, #2ce7e4 100%);
  background: linear-gradient(to bottom, #2400b2 0%, #2ce7e4 100%);
  -webkit-background-clip: text;
  -moz-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.v-icon.bolt {
  background: -moz-linear-gradient(top, #ff7b00 0%, #fff700 100%);
  background: -webkit-linear-gradient(top, #ff7b00 0%, #fff700 100%);
  background: linear-gradient(to bottom, #aaaaaa 0%, #fff700 100%);
  -webkit-background-clip: text;
  -moz-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.v-icon.camera {
  background: -moz-linear-gradient(left, #95a3b0 0%, #4a6b9d 100%);
  background: -webkit-linear-gradient(left, #95a3b0 0%, #4a6b9d 100%);
  background: linear-gradient(to right, #95a3b0 0%, #4a6b9d 100%);
  -webkit-background-clip: text;
  -moz-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
</style>