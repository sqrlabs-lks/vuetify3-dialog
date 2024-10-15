<script setup lang="ts">
import { ref, computed, onMounted, nextTick } from 'vue';
import { VBtn, VCard, VCardActions, VCardText, VCardTitle, VIcon } from 'vuetify/lib/components/index.mjs';

const props = defineProps({
  title: {
    type: String,
    required: true
  },
  text: {
    type: String,
    required: true
  },
  buttons: {
    type: Array as () => any[],
  },
  icon: {
    type: String,
    default: ''
  },
  level: {
    type: String as () => 'info' | 'warning' | 'error' | 'success',
    default: 'info'
  },
  cardOptions: {
    type: Object,
    default: () => ({})
  }
})

// ------- REFS -------
const okButtonRef = ref()
const cancelButtonRef = ref()

// ------- EVENTS -------
const emit = defineEmits(['buttonClicked'])

// ------- COMPUTED -------
const _buttons = computed(() => {
  if(props.buttons && props.buttons.length > 0) return props.buttons
  else return [
    { key: 'cancel', ref: 'cancelButtonRef', title: 'Annuler', value: 'cancel', color: 'grey', variant: 'text' },
    { key: 'ok', ref: 'okButtonRef', title: '확인', value: 'ok', color: props.level, variant: 'tonal' }
  ]
})

const _icon = computed(() => {
  if (props.icon) return props.icon;
  switch (props.level) {
    case 'info':
      return 'mdi-information'
    case 'warning':
      return 'mdi-alert'
    case 'error':
      return 'mdi-alert-circle'
    case 'success':
      return 'mdi-check-circle'
    default:
      return 'mdi-information'
  }
})

const _color = computed(() => {
  return props.level === 'info' ? 'primary' : props.level
})

// ------- METHODS -------
function close(buttonKey: string | boolean){
  emit('buttonClicked', buttonKey)
}

// ------- EVENTS -------
onMounted(() => {
  nextTick(() => {
    setTimeout(() => {
      okButtonRef.value?.focus()
      window.console.log('ok button focused')
    }, 500)
  })
})
</script>

<template>
  <VCard class="vuetify3-dialog-card" v-bind="cardOptions">
    <VCardTitle class="d-flex align-center"><VIcon :color="_color" class="mr-2">{{_icon}}</VIcon>{{title}}</VCardTitle>
    <VCardText>{{text}}</VCardText>
    <VCardActions>
      <VBtn
        v-for="button in _buttons"
        :key="button.key"
        :ref="button.ref"
        v-bind="button"
        :color="button.color || _color"
        @click="close(button.key)"
      >
        {{button.title}}
      </VBtn>
    </VCardActions>
  </VCard>
</template>
