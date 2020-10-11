<template>
    <div>Tabs示例</div>
    <h1>示例1</h1>
    <Tabs>
        <Tab title="导航1">内容1</Tab>
        <Tab title="导航2">内容1</Tab>
    </Tabs>
</template>

<script lang="ts">
import Tabs from '../../src/lib/Tabs.vue';
import Tab from '../../src/lib/Tab.vue';

export default {
    components: {
        Tabs,
        Tab,
    },
};
</script>
