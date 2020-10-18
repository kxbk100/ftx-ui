<template>
    <div>
        <router-link to="/" class="topnav">
            <div class="logo">
                <svg class="icon" aria-hidden="true">
                    <use xlink:href="#icon-feng"></use>
                </svg>
            </div>
            <ul class="menu">
                <li>菜单1</li>
                <li>菜单2</li>
            </ul>
            <svg
                class="toggleAside"
                @click="toggleMenu"
                v-if="toggleMenuButtonVisible"
            >
                <use xlink:href="#icon-menu"></use>
            </svg>
        </router-link>
    </div>
</template>

<script lang="ts">
import { inject, Ref } from 'vue';
export default {
    props: {
        toggleMenuButtonVisible: {
            type: Boolean,
            default: false
        }
    },
    setup() {
        // TS声明类型
        const menuVisible = inject<Ref<boolean>>('menuVisible'); // get
        const toggleMenu = () => {
            menuVisible.value = !menuVisible.value;
        };
        return { toggleMenu };
    }
};
</script>

<style lang="scss" scoped>
.topnav {
    display: flex;
    padding: 16px;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 20;
    justify-content: center;
    align-items: center;

    > .logo {
        max-width: 6em;
        margin-right: auto;

        > svg {
            width: 40px;
            height: 40px;
        }
    }

    > .menu {
        display: flex;
        white-space: nowrap;
        flex-wrap: nowrap;
        > li {
            margin: 0 1em;
        }
    }

    > .toggleAside {
        width: 32px;
        height: 32px;
        position: absolute;
        left: 16px;
        top: 50%;
        transform: translateY(-50%);
        display: none;
        background: fade-out(black, 0.9);
    }

    @media (max-width: 500px) {
        > .menu {
            display: none;
        }
        > .logo {
            margin: 0 auto;
        }
        > .toggleAside {
            display: inline-block;
        }
    }
}
</style>
