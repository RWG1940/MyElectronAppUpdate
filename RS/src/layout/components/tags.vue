<template>
    <transition name="t-trans" appear>
        <el-scrollbar class="eb">
            <t-tag theme="primary" :variant="item.isActive ? 'dark' : 'light'" class="menu-tags" closable
                v-for="(item, index) in tagData" :key="item.name + index" @close="handleClose(item)"
                @click="handleSelect(item)" @contextmenu.prevent="onRightClick">
                <span><t-icon :name="item.meta.icon" style="margin-top: -1px;"></t-icon>{{ item.name }}</span>
            </t-tag>
        </el-scrollbar>
    </transition>
</template>
  
<script lang="ts" setup>
import { onMounted, computed } from 'vue';
import { useTagsStore } from '@/stores/tags-store';
import router from '@/router';

const tagsStore = useTagsStore();

// 计算标签数据
const tagData = computed(() => {
    return tagsStore.tagsList;
});

const handleClose = (item: any) => {
    tagsStore.delRoute(item);
};

const handleSelect = (item: any) => {
    if (item.name !== tagsStore.currentRoute?.name) {
        router.push(item.path);
        tagsStore.setTagsList(item);
    }
};
const onRightClick = (e: any) => {

};

</script>
  
<style lang="less" scoped>
.menu-tags {
    margin-right: 6px;
    margin-bottom: 2px;
    transition-duration: 0.5s;
}


.menu-tags:hover {
    background-color: rgb(0, 187, 255);
    color:rgb(255, 255, 255);
    box-shadow: 1px 1px 6px rgb(167, 205, 255);
}

.t-trans-enter-active,
.t-trans-leave-active {
    transition: all 0.5s ease-out;
}

.t-trans-enter-from,
.t-trans-leave-to {
    opacity: 0;
    transform: translateY(-200px);
}

.eb {
    cursor: pointer;
    padding:0.5% 1.5%;
    transition-duration: 0.5s;
}</style>