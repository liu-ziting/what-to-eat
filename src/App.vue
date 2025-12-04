<template>
    <div id="app" class="min-h-screen">
        <router-view />
        <!-- <FloatingDonation /> -->
        <GlobalNoticeModal />
    </div>
</template>

<script setup>
// App组件
// import FloatingDonation from './components/FloatingDonation.vue'
import GlobalNoticeModal from './components/GlobalNoticeModal.vue'
</script>
