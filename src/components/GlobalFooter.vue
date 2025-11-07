<template>
    <footer class="bg-white border-2 border-[#0A0910] max-w-7xl mx-auto px-2 rounded-lg p-4 text-center mt-8">
        <div class="space-y-3">
            <!-- 主要信息 -->
            <div class="flex flex-col md:flex-row items-center justify-center gap-2 md:gap-4">
                <p class="text-sm text-gray-600">
                    © 2025 一饭封神 |
                    <a href="https://github.com/liu-ziting/what-to-eat" target="_blank" class="text-blue-600 hover:text-blue-700 hover:underline transition-colors"> Github </a>
                </p>
                <div class="hidden md:block w-px h-4 bg-gray-300"></div>
                <p class="text-xs text-gray-500">🚀 Vibe Coding With Kiro AI</p>
            </div>
        </div>
    </footer>
</template>

<script setup lang="ts">
// Footer组件，无需额外逻辑
</script>

<style scoped>
/* 确保链接的悬停效果 */
a {
    transition: all 0.2s ease-in-out;
}

a:hover {
    transform: translateY(-1px);
}

/* 响应式调整 */
@media (max-width: 640px) {
    .space-y-3 > * + * {
        margin-top: 0.5rem;
    }
}
</style>
