<template>
    <div class="ftx-tabs">
        <div class="ftx-tabs-nav" ref="container">
            <!-- 使用v-for就要写key -->
            <div
                class="ftx-tabs-nav-item"
                v-for="(t, index) in titles"
                :ref="
                    (el) => {
                        if (t === selected) selectedItem = el;
                    }
                "
                @click="select(t)"
                :class="{ selected: t === selected }"
                :key="index"
            >
                {{ t }}
            </div>
            <div class="ftx-tabs-nav-indicator" ref="indicator"></div>
        </div>
        <div class="ftx-tabs-content">
            <!-- 虚拟节点需要用component is来展示 -->
            <!-- 用component实现一个slot -->
            <!-- 嵌套渲染插槽的时候可以这么写 -->
            <component :is="current" :key="current.props.title" />
        </div>
    </div>
</template>

<script lang="ts" setup="props, context">
import Tab from './Tab.vue';
import {
    computed,
    ref,
    watchEffect,
    onMounted,
    SetupContext,
    Component
} from 'vue';

declare const props: { selected: string };
declare const context: SetupContext;

export default {
    props: {
        selected: {
            type: String
        }
    }
};

// ts泛型
export const selectedItem = ref<HTMLDivElement>(null);
export const indicator = ref<HTMLDivElement>(null);
export const container = ref<HTMLDivElement>(null);

// onMounted只在第一次渲染的时候执行
// watchEffect(x) = onMounted(x) + onUpdated(x);
onMounted(() => {
    watchEffect(
        // Vue的div操作

        () => {
            const { width } = selectedItem.value.getBoundingClientRect();
            indicator.value.style.width = width + 'px';
            const { left: left1 } = container.value.getBoundingClientRect();
            const { left: left2 } = selectedItem.value.getBoundingClientRect();
            const left = left2 - left1;
            indicator.value.style.left = left + 'px';
        },
        {
            flush: 'post'
        }
    );
});

export const defaults = context.slots.default();
defaults.forEach((tag) => {
    if ((tag.type as Component).name !== Tab.name) {
        throw new Error('Tabs 子标签必须是 Tab');
    }
});
export const current = computed(() => {
    return defaults.find((tag) => tag.props.title === props.selected);
});
export const titles = defaults.map((tag) => {
    return tag.props.title;
});
export const select = (title: string) => {
    context.emit('update:selected', title);
};
</script>

<style lang="scss">
$blue: #40a9ff;
$color: #333;
$border-color: #d9d9d9;
.ftx-tabs {
    &-nav {
        display: flex;
        color: $color;
        border-bottom: 1px solid $border-color;
        position: relative;
        &-item {
            padding: 8px 0;
            margin: 0 16px;
            cursor: pointer;
            &:first-child {
                margin-left: 0;
            }
            &.selected {
                color: $blue;
            }
        }
        &-indicator {
            position: absolute;
            height: 3px;
            background: $blue;
            left: 0;
            bottom: -1px;
            width: 100px;
            transition: all 250ms;
        }
    }
    &-content {
        padding: 8px 0;
    }
}
</style>
