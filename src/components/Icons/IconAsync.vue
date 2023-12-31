<template>
  <IconAsyncComponent />
</template>

<script lang="ts" setup>
import { Component, defineAsyncComponent } from 'vue'
import { IconName, HeroIconName, IconType } from '../../types'
import { ExclamationCircleIcon, ArrowPathIcon } from '@heroicons/vue/24/solid'

interface Props {
  name: IconName
  type?: IconType
}

const props = withDefaults(defineProps<Props>(), {
  name: 'ExclamationCircleIcon',
  type: 'solid',
})

const IconAsyncComponent = defineAsyncComponent({
  loader: async () => {
    try {
      // Enable the next line to mimic a long load time
      // await new Promise((resolve) => setTimeout(resolve, 2000))
      // OR enable this line to mimic failing due to timing out
      // await new Promise((resolve) => setTimeout(resolve, 5000))

      let iconComponent: Component | undefined

      if (props.type === 'custom') {
        iconComponent = (await import(`./Custom/${props.name}.vue`)) as Component
      } else if (props.type === 'outline') {
        const module = await import('@heroicons/vue/24/outline')
        iconComponent = module[props.name as HeroIconName] as Component
      } else {
        const module = await import('@heroicons/vue/24/solid')
        iconComponent = module[props.name as HeroIconName] as Component
      }

      if (iconComponent === undefined) {
        throw new Error()
      }

      return iconComponent
    } catch (error) {
      throw new Error('Failed to load component.')
    }
  },
  loadingComponent: ArrowPathIcon,
  delay: 200,
  errorComponent: ExclamationCircleIcon,
  timeout: 4000,
})
</script>
