<script setup lang="ts">
  import type { useEditor } from '@/views/Editor/app'
  import { Trace } from '@/views/Editor/core/instantiation/instantiationService'

  const { canvas } = inject<typeof useEditor>('useEditor')!()

  const printTrace = () => {
    if (Trace.all.size === 0) {
      console.log('Enable via `instantiationService.ts#_enableAllTracing`')
      return
    }

    for (const item of Trace.all) {
      console.log(item)
    }
  }
  const recommendDish = () => {
    console.log(canvas.activeObject)
    canvas.contentFrame.width = 1200
    canvas.contentFrame.height = 900
    console.log(canvas.contentFrame.children)

    setTimeout(() => {
        canvas.childrenEffect()
    }, 200)
  }
</script>

<template>
  <div class="p2">
    <div>测试插件插槽</div>
    <a-space>
        <a-button @click="recommendDish">商户通->推荐菜</a-button>
        
    </a-space>
  </div>
</template>
