<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { getAuth, onAuthStateChanged } from 'firebase/auth'
import { useUserStore } from '@/stores/user'
import AppHeader from '@/components/AppHeader.vue'

const userStore = useUserStore()
const isLoading = ref<boolean>(true)

onMounted(() => {
    onAuthStateChanged(getAuth(), (user) => {
        if (user) {
            userStore.userId = user.uid
        } else {
            userStore.userId = ''
        }
        isLoading.value = false
    })
})
</script>

<template>
    <app-progress v-if="isLoading" class="flex align-items-center h-full"></app-progress>
    <div v-else class="container">
        <AppHeader />
        <div class="content">
            <RouterView />
        </div>
    </div>
</template>

<style scoped>
.container {
    max-width: 1280px;
    height: 100%;
    margin: auto;
    padding: 20px;
}
</style>
