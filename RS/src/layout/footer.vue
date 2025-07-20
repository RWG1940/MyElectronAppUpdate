<template>
   <t-footer style="background-color: rgba(146, 146, 146, 0.16);padding:10px">
               <t-breadcrumb style="background-color: rgba(146, 146, 146, 0.16);padding:5px;border-radius:10px" >
                  <t-icon name="system-location"></t-icon>当前位置：
                  <t-breadcrumb-item v-for="(item, index) in $route.matched" :key="index" :to="item.path"
                     :replace="true">{{
                        item.name }}</t-breadcrumb-item>
               </t-breadcrumb>
   </t-footer>
</template>
<script lang="ts">
export default {

}
</script>
<style scoped>

</style>