<template>
    <div v-if="show" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4" @click.self="handleClose(false)">
        <div class="bg-white rounded-lg border-2 border-[#0A0910] max-w-md w-full animate-fade-in">
            <div class="border-b-2 border-black p-4 bg-gradient-to-r from-orange-50 to-yellow-50">
                <h3 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <span class="text-2xl">📢</span>
                    重要通知
                </h3>
            </div>
            <div class="p-6">
                <div class="space-y-3 text-gray-700 mb-6">
                    <p class="font-medium text-base">尊敬的用户：</p>
                    <p class="leading-relaxed">
                        目前提供的是<span class="font-semibold text-purple-600">智谱清言</span
                        >免费文本生成模型（glm-4v-flash）和图片生成模型（cogview-3-flash），菜谱生成效果并不理想。
                    </p>
                    <p class="leading-relaxed">
                        建议在<span class="font-semibold text-orange-600">设置</span>中配置更优质的模型，如：<span class="font-semibold text-blue-600">DeepSeek</span>、<span
                            class="font-semibold text-blue-600"
                            >豆包</span
                        >等，或自行部署模型服务，以获得更好的使用体验。
                    </p>
                </div>
                <div class="flex gap-3">
                    <button
                        @click="handleClose(false)"
                        class="flex-1 px-4 py-3 bg-gray-500 hover:bg-gray-600 text-white rounded-lg font-medium border-2 border-[#0A0910] transition-all duration-200 shadow-md hover:shadow-lg"
                    >
                        我知道了
                    </button>
                    <button
                        @click="handleClose(true)"
                        class="flex-1 px-4 py-3 bg-gradient-to-r from-orange-500 to-yellow-500 hover:from-orange-600 hover:to-yellow-600 text-white rounded-lg font-medium border-2 border-[#0A0910] transition-all duration-200 shadow-md hover:shadow-lg"
                    >
                        今日不再提醒
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const show = ref(false)
const NOTICE_KEY = 'global-notice-dismissed'

onMounted(() => {
    const dismissed = localStorage.getItem(NOTICE_KEY)
    if (dismissed !== 'permanent') {
        // 延迟显示，让页面先加载
        setTimeout(() => {
            show.value = true
        }, 500)
    }
})

const handleClose = (permanent: boolean) => {
    show.value = false
    if (permanent) {
        // 永久关闭
        localStorage.setItem(NOTICE_KEY, 'permanent')
    }
    // 如果不是永久关闭，不设置localStorage，下次访问还会显示
}
</script>

<style scoped>
@keyframes fade-in {
    from {
        opacity: 0;
        transform: scale(0.95);
    }
    to {
        opacity: 1;
        transform: scale(1);
    }
}

.animate-fade-in {
    animation: fade-in 0.3s ease-out;
}
</style>
