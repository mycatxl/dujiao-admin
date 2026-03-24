<script setup lang="ts">
import { computed } from 'vue'
import { storeToRefs } from 'pinia'
import { useConfirmStore, type ConfirmDialogDescriptionSegment } from '@/stores/confirm'
import { Button } from '@/components/ui/button'
import { Dialog, DialogContent, DialogDescription, DialogFooter, DialogHeader, DialogTitle } from '@/components/ui/dialog'

const confirmStore = useConfirmStore()
const { open, options } = storeToRefs(confirmStore)

const confirmVariant = computed(() => (options.value.variant === 'destructive' ? 'destructive' : 'default'))
const descriptionSegments = computed<ConfirmDialogDescriptionSegment[]>(() => {
  const description = options.value.description
  if (Array.isArray(description)) return description
  return [{ text: description }]
})

const segmentClass = (segment: ConfirmDialogDescriptionSegment) => {
  if (segment.tone === 'danger') return 'text-destructive'
  if (segment.tone === 'muted') return 'text-muted-foreground'
  return 'text-foreground'
}
</script>

<template>
  <Dialog :open="open" @update:open="confirmStore.setOpen">
    <DialogContent class="sm:max-w-md">
      <DialogHeader>
        <DialogTitle>{{ options.title }}</DialogTitle>
        <DialogDescription>
          <span
            v-for="(segment, index) in descriptionSegments"
            :key="index"
            :class="[segmentClass(segment), segment.strong ? 'font-semibold' : 'font-normal']"
          >
            {{ segment.text }}
          </span>
        </DialogDescription>
      </DialogHeader>
      <DialogFooter>
        <Button variant="outline" @click="confirmStore.cancel">{{ options.cancelText }}</Button>
        <Button :variant="confirmVariant" @click="confirmStore.confirm">{{ options.confirmText }}</Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>
