<template>
  <Teleport :disabled="!toBody" v-if="modalShow">
    <Transition appear name="bg">
      <div
          v-if="show && !hidden && isBg"
          class="modal-bg"
          @click="emit('update:show', false)"
      />
    </Transition>
    <div
        class="rv-sheet"
        ref="RvSheetRef"
    >
      <div class="rv-sheet__header">
        <div class="header-container">
          <div class="drag-bar" />
        </div>
        <slot name="header" />
      </div>
      <div class="rv-sheet__content" @scroll="emit('scroll', $event)">
        <slot />
        <div class="spacer"></div>
      </div>
    </div>
  </Teleport>
</template>

<script setup lang="ts">
import { ref, watch, onMounted, onUnmounted } from 'vue'
import { RvSheet, RvSheetProps } from '@reevo-design/sheet'
import '@reevo-design/sheet/style.css'

const props = withDefaults(defineProps<RvSheetProps & { toBody?: boolean; hidden?: boolean; isBg?: boolean }>(), {
  allowDragSheetContent: true,
  canClose: true,
  allowShortSwipes: true,
  topOffset: undefined,
  toBody: false,
  hidden: false,
  isBg: false,
  hideScrollOnBody: true,
})
const emit = defineEmits(['update:show', 'scroll'])

const RvSheetRef = ref<HTMLElement>()
const sheet = ref<RvSheet>()
const modalShow = ref(props.show)
defineExpose({ sheet })

async function initModal() {
  if (props.show) {
    modalShow.value = true
    requestAnimationFrame(() => {
      if (RvSheetRef.value) {
        sheet.value = new RvSheet(RvSheetRef.value, { ...props })
        sheet.value.on('closed', () => {
          if (!props.hidden) {
            modalShow.value = false
            emit('update:show', false)
            sheet.value = undefined
          }
        })
      }
    })
  } else if (sheet.value) {
    sheet.value.canClose = true
    if (sheet.value.show) {
      sheet.value?.closeSheet()
    } else {
      modalShow.value = false
      emit('update:show', false)
      sheet.value = undefined
    }
  } else {
    emit('update:show', false)
  }
}

onMounted(() => {
  initModal()
})

watch(() => props.show, initModal)
watch(
    () => props.hidden,
    (v) => {
      if (v) {
        sheet.value?.closeSheet()
      } else {
        sheet.value?.showSheet()
      }
    }
)
</script>

<style scoped>
.rv-sheet {
  top: 16px !important;
}

.bg-enter-active,
.bg-leave-active {
  transition: opacity 0.25s ease;
}

.bg-enter-from,
.bg-leave-to {
  opacity: 0;
}

/* Modal background */
.modal-bg {
  background-color: rgba(0, 0, 0, 0.25); /* Transparent dark */
  backdrop-filter: blur(8px);
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100vw;
  cursor: pointer;
  z-index: 2000;
}

/* Sheet */
.rv-sheet {
  border-radius: 1rem 1rem 0 0; /* Large rounded corners for top-left and top-right */
  z-index: 2001;
}

/* Sheet header */
.rv-sheet__header {
  position: relative;
}

/* Header container for positioning */
.header-container {
  position: absolute;
  left: 0;
  top: 0;
  display: flex;
  height: 1rem;
  width: 100%;
  justify-content: center;
  align-items: center;
}

/* Drag bar (style for the handle to drag the sheet) */
.drag-bar {
  background-color: rgba(128, 128, 128, 0.25);
  border-radius: 2px;
  height: 0.25rem;
  width: 2.25rem;
  cursor: pointer;
}

/* Spacer for spacing at the bottom */
.spacer {
  height: 1rem;
}
</style>

