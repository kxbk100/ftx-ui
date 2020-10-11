<template>
    <div class="gulu-tabs">
        <div class="gulu-tabs-nav" ref="container">
            <!-- 使用v-for就要写key -->
            <div
                class="gulu-tabs-nav-item"
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
            <div class="gulu-tabs-nav-indicator" ref="indicator"></div>
        </div>
        <div class="gulu-tabs-content">
            <!-- 虚拟节点需要用component is来展示 -->
            <!-- 用component实现一个slot -->
            <!-- 嵌套渲染插槽的时候可以这么写 -->
            <component :is="current" :key="current.props.title" />
        </div>
    </div>
</template>

<script lang="ts">
import { computed, onMounted, ref, watchEffect } from 'vue';
import Tab from './Tab.vue';
export default {
    props: {
        selected: {
            type: String,
            default: '',
        },
    },
    setup(props, context) {
        // ts泛型
        const selectedItem = ref<HTMLDivElement>(null);
        const indicator = ref<HTMLDivElement>(null);
        const container = ref<HTMLDivElement>(null);
        const defaults = context.slots.default();
        const current = computed(() => {
            return defaults.filter(
                (tag) => tag.props.title === props.selected
            )[0];
        });
        // onMounted只在第一次渲染的时候执行
        // watchEffect(x) = onMounted(x) + onUpdated(x);

        onMounted(() => {
            watchEffect(() => {
                // Vue的div操作
                const {
                    width,
                    left: left1,
                } = selectedItem.value.getBoundingClientRect();
                indicator.value.style.width = width + 'px';
                const { left: left2 } = container.value.getBoundingClientRect();
                const left = left1 - left2;
                indicator.value.style.left = left + 'px';
            });
        });

        defaults.forEach((tag) => {
            if (tag.type !== Tab) {
                throw new Error('Tabs 子标签必须是 Tab');
            }
        });
        const titles = defaults.map((tag) => {
            return tag.props.title;
        });
        const select = (title: String) => {
            context.emit('update:selected', title);
        };
        return {
            indicator,
            defaults,
            titles,
            select,
            selectedItem,
            container,
            current,
        };
    },
};
</script>

<style lang="scss">
$blue: #40a9ff;
$color: #333;
$border-color: #d9d9d9;
.gulu-tabs {
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
