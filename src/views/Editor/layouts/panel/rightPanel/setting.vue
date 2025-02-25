<script setup lang="ts">
import {isDefined, useResizeObserver} from "@vueuse/core";

import BaseAttr from "./attrs/baseAttr.vue";
import LayerAttr from "./attrs/layerAttr.vue";
import TextAttr from "./attrs/textAttr.vue";
import HtmlTextAttr from "./attrs/htmlTextAttr.vue";
import ImageAttr from "./attrs/imageAttr.vue";
import CanvasAttr from './attrs/canvasAttr.vue'
import BoxAttr from './attrs/boxAttr.vue'
import FillAttr from "./attrs/fillAttr.vue";
import StrokeAttr from "./attrs/strokeAttr.vue";
import VirtualElementAttr from "./attrs/VirtualElementAttr.vue";
import {appInstance, useEditor} from "@/views/Editor/app";
import {typeUtil} from "@/views/Editor/utils/utils";

const {editor} = useEditor()
const splitRef = ref()
const treeHeight = ref(0)

onMounted(() => {
    // 更新tree组件的高度
    useResizeObserver(splitRef.value as HTMLDivElement, (entries) => {
        const [entry] = entries
        const {height} = entry.contentRect
        treeHeight.value = height - 43
    })
})

const componentList = computed(() => {
    const activeObject = editor.activeObject.value
    return [
        {
            name: 'CanvasAttr',
            component: CanvasAttr,
            visual: typeUtil.isBottomCanvas(activeObject),
        },
        {
            name: 'VirtualElementAttr',
            component: VirtualElementAttr,
            visual: typeUtil.isVirtualElement(activeObject),
        },
        {
            name: 'BaseAttr',
            component: BaseAttr,
            visual: !typeUtil.isVirtualOrBottom(activeObject),
        },
        {
            name: 'LayerAttr',
            component: LayerAttr,
            visual: isDefined(activeObject) && !typeUtil.isVirtualOrBottom(activeObject),
        },
        {
            name: 'BoxAttr',
            component: BoxAttr,
            visual: editor.activeObjectIsType('Box'),
        },
        {
            name: 'TextAttr',
            component: TextAttr,
            visual: isDefined(activeObject) && editor.activeObjectIsType('Text'),
        },
        {
            name: 'HtmlTextAttr',
            component: HtmlTextAttr,
            visual: isDefined(activeObject) && editor.activeObjectIsType('HTMLText'),
        },
        {
            name: 'ImageAttr',
            component: ImageAttr,
            visual: isDefined(activeObject) && editor.activeObjectIsType('Image'),
        },
        {
            name: 'FillAttr',
            component: FillAttr,
            visual:
                isDefined(activeObject)
                &&!typeUtil.isVirtualOrBottom(activeObject)
                && !editor.activeObjectIsType('Image','Pen','HTMLText')
                ,
        },
        {
            name: 'StrokeAttr',
            component: StrokeAttr,
            visual:
                isDefined(activeObject)
                &&!typeUtil.isVirtualOrBottom(activeObject)
                && !editor.activeObjectIsType('Pen')
        },
        // {
        //     name: 'StrokeAttr',
        //     component: StrokeAttr,
        //     visual: isDefined(activeObject) && !util.isCollection(activeObject),
        // },
        // 阴影
        // 模糊
    ]
})

const pluginSolts = appInstance.editor.getPluginSlots('rightPanel')
</script>

<template>
    <div
            ref="splitRef"
            class="ovf">
        <div>
            <template v-for="(com, index) in componentList" :key="com.name">
                <template v-if="com.visual">
                    <a-divider v-if="index !== 0" :margin="0" />
                    <component :is="com.component" />
                </template>
            </template>
            <template v-for="(com, index) in pluginSolts" :key="index">
                <a-divider v-if="index !== com.length - 1" :margin="0" />
                <component :is="com" />
            </template>
        </div>
    </div>
</template>

<style scoped lang="less">
.ovf{
    height: calc(100vh - 95px);
    overflow-y: auto;
    overflow-x: hidden;
}
</style>
