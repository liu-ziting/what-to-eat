<template>
    <div v-if="isVisible" class="fixed inset-0 bg-black bg-opacity-50 z-50">
        <!-- 轻提示 -->
        <div
            v-if="toast.show"
            class="fixed top-4 left-1/2 transform -translate-x-1/2 z-60 bg-white border border-gray-200 rounded-lg shadow-lg px-4 py-3 flex items-center gap-2 transition-all duration-300"
        >
            <svg v-if="toast.type === 'success'" class="w-5 h-5 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
            </svg>
            <svg v-if="toast.type === 'info'" class="w-5 h-5 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
            </svg>
            <span class="text-sm text-gray-700">{{ toast.message }}</span>
        </div>

        <div class="bg-white w-full h-full flex flex-col md:flex-row">
            <!-- 移动端：显示全局头部 -->
            <div class="md:hidden flex justify-between items-center px-4 py-3 border-b border-gray-200 bg-white flex-shrink-0">
                <h2 class="text-lg font-semibold text-gray-800">模型配置</h2>
                <button @click="closeModal" class="text-gray-400 hover:text-gray-600 p-1">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                </button>
            </div>

            <!-- 主要内容区域 -->
            <div class="flex-1 md:flex md:h-full overflow-hidden">
                <!-- 移动端：垂直滚动布局，PC端：左右布局 -->
                <div class="h-full md:flex md:w-full md:overflow-hidden">
                    <!-- 移动端和PC端的统一滚动容器 -->
                    <div class="h-full overflow-y-auto md:flex md:w-full md:h-full">
                        <!-- 左侧：推荐广告区域 -->
                        <div
                            v-if="false"
                            class="md:w-1/3 bg-gradient-to-br from-indigo-600 via-purple-600 to-pink-500 p-4 md:p-6 border-b md:border-b-0 md:border-r border-purple-300 md:flex-shrink-0 md:h-full relative overflow-hidden"
                        >
                            <!-- 背景装饰 -->
                            <div class="absolute inset-0 bg-gradient-to-br from-white/10 to-transparent"></div>
                            <div class="absolute -top-10 -right-10 w-32 h-32 bg-white/5 rounded-full"></div>
                            <div class="absolute -bottom-6 -left-6 w-24 h-24 bg-white/5 rounded-full"></div>

                            <div class="relative z-10 md:h-full md:flex md:flex-col md:justify-center">
                                <div class="text-center text-white">
                                    <!-- Logo区域 -->
                                    <div class="mb-4 md:mb-6">
                                        <div
                                            class="w-16 h-16 md:w-24 md:h-24 mx-auto mb-3 bg-white/20 backdrop-blur-sm rounded-2xl flex items-center justify-center border border-white/30 shadow-xl"
                                        >
                                            <span class="text-2xl md:text-4xl font-bold bg-gradient-to-br from-yellow-300 via-orange-300 to-pink-300 bg-clip-text text-transparent"
                                                >AI</span
                                            >
                                        </div>
                                        <div class="inline-flex items-center gap-2 bg-white/20 backdrop-blur-sm px-3 py-1 rounded-full border border-white/30">
                                            <div class="w-2 h-2 bg-green-400 rounded-full animate-pulse"></div>
                                            <span class="text-xs font-medium">一叶知秋API</span>
                                        </div>
                                    </div>

                                    <!-- 主标题 -->
                                    <h3 class="text-lg md:text-2xl font-bold mb-2 md:mb-3">
                                        <span class="bg-gradient-to-r from-yellow-300 to-orange-300 bg-clip-text text-transparent"> 🚀 推荐API服务商 </span>
                                    </h3>

                                    <!-- 描述文字 -->
                                    <div class="mb-4 md:mb-6">
                                        <p class="text-sm md:text-base text-white/90 mb-3 leading-relaxed">
                                            <!-- <strong class="text-yellow-300">一叶知秋：</strong> -->
                                            致力于提供可持续且高质量的服务，运用先进的技术架构保障API服务的稳定性与高可用性。
                                        </p>

                                        <!-- 特色标签 -->
                                        <div class="flex flex-wrap gap-2 justify-center mb-4">
                                            <span class="bg-gradient-to-r from-green-400 to-emerald-500 text-white text-xs px-3 py-1 rounded-full font-medium shadow-lg">
                                                ✨ 最新模型
                                            </span>
                                            <span class="bg-gradient-to-r from-blue-400 to-cyan-500 text-white text-xs px-3 py-1 rounded-full font-medium shadow-lg">
                                                💰 按量付费
                                            </span>
                                            <span class="bg-gradient-to-r from-purple-400 to-pink-500 text-white text-xs px-3 py-1 rounded-full font-medium shadow-lg">
                                                🛡️ 透明计费
                                            </span>
                                        </div>
                                    </div>

                                    <!-- 优势列表 -->
                                    <div class="hidden md:block mb-6">
                                        <div class="bg-white/10 backdrop-blur-sm rounded-xl p-4 border border-white/20">
                                            <div class="grid grid-cols-1 gap-3 text-left">
                                                <div class="flex items-center gap-3">
                                                    <div class="w-6 h-6 bg-green-400 rounded-full flex items-center justify-center flex-shrink-0">
                                                        <svg class="w-3 h-3 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path>
                                                        </svg>
                                                    </div>
                                                    <span class="text-sm text-white/90">支持GPT、Claude、Gemini等主流模型</span>
                                                </div>
                                                <div class="flex items-center gap-3">
                                                    <div class="w-6 h-6 bg-blue-400 rounded-full flex items-center justify-center flex-shrink-0">
                                                        <svg class="w-3 h-3 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path>
                                                        </svg>
                                                    </div>
                                                    <span class="text-sm text-white/90">0月费和按用量付费模式</span>
                                                </div>
                                                <div class="flex items-center gap-3">
                                                    <div class="w-6 h-6 bg-purple-400 rounded-full flex items-center justify-center flex-shrink-0">
                                                        <svg class="w-3 h-3 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path>
                                                        </svg>
                                                    </div>
                                                    <span class="text-sm text-white/90">最新、最全面的AI模型和API</span>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <!-- CTA按钮 -->
                                    <a
                                        href="https://88996.cloud/register?aff=Vswe"
                                        target="_blank"
                                        class="group inline-flex items-center gap-2 bg-gradient-to-r from-yellow-400 to-orange-500 hover:from-yellow-500 hover:to-orange-600 text-gray-900 px-6 py-3 md:px-8 md:py-4 rounded-xl font-bold transition-all duration-300 transform hover:scale-105 shadow-xl hover:shadow-2xl"
                                    >
                                        <span class="text-sm md:text-base">🎯 立即体验</span>
                                        <svg class="w-4 h-4 group-hover:translate-x-1 transition-transform" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6"></path>
                                        </svg>
                                    </a>

                                    <!-- 优惠提示 -->
                                    <div class="mt-3 md:mt-4">
                                        <div class="inline-flex items-center gap-2 bg-red-500/20 backdrop-blur-sm px-3 py-1 rounded-full border border-red-400/30">
                                            <span class="text-xs text-red-200 animate-pulse">🔥</span>
                                            <span class="text-xs text-red-200 font-medium">新用户专享优惠</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- 右侧：配置表单区域 -->
                        <div class="md:flex-1 md:h-full md:flex md:flex-col p-4 md:p-0">
                            <!-- PC端：右侧头部 -->
                            <div class="hidden md:flex justify-between items-center px-6 py-4 border-b border-gray-200 bg-white flex-shrink-0">
                                <h2 class="text-xl font-semibold text-gray-800">模型配置</h2>
                                <button @click="closeModal" class="text-gray-400 hover:text-gray-600 p-1">
                                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                                    </svg>
                                </button>
                            </div>

                            <!-- 配置内容区域 - 可滚动 -->
                            <div class="w-full space-y-6 md:flex-1 md:overflow-y-auto md:px-6 md:py-4 md:pr-8">
                                <!-- 菜谱生成模型配置 -->
                                <div class="bg-white border border-gray-200 rounded-lg p-4 md:p-6">
                                    <h3 class="text-lg font-semibold text-gray-800 flex items-center mb-4">
                                        <svg class="w-5 h-5 mr-3 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path
                                                stroke-linecap="round"
                                                stroke-linejoin="round"
                                                stroke-width="2"
                                                d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"
                                            ></path>
                                        </svg>
                                        菜谱生成模型配置
                                    </h3>

                                    <div class="space-y-4">
                                        <!-- API地址 -->
                                        <div>
                                            <label class="block text-sm font-medium text-gray-700 mb-2">API地址</label>
                                            <input
                                                v-model="textConfig.baseUrl"
                                                type="text"
                                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm"
                                                placeholder="https://api.example.com/v1/"
                                            />
                                            <p class="text-xs text-gray-500 mt-1">基础API地址，系统会自动添加 /chat/completions 路径</p>
                                        </div>

                                        <!-- 配置项网格 -->
                                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                            <div>
                                                <label class="block text-sm font-medium text-gray-700 mb-2">API密钥</label>
                                                <input
                                                    v-model="textConfig.apiKey"
                                                    type="password"
                                                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm"
                                                    placeholder="输入API密钥"
                                                />
                                            </div>

                                            <div>
                                                <label class="block text-sm font-medium text-gray-700 mb-2">模型名称</label>
                                                <input
                                                    v-model="textConfig.model"
                                                    type="text"
                                                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm"
                                                    placeholder="yi-lightning"
                                                />
                                            </div>

                                            <div>
                                                <label class="block text-sm font-medium text-gray-700 mb-2">温度参数 (0-1)</label>
                                                <input
                                                    v-model.number="textConfig.temperature"
                                                    type="number"
                                                    min="0"
                                                    max="1"
                                                    step="0.1"
                                                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm"
                                                />
                                                <p class="text-xs text-gray-500 mt-1">控制回答的创造性，0.7为推荐值</p>
                                            </div>

                                            <div>
                                                <label class="block text-sm font-medium text-gray-700 mb-2">超时时间 (毫秒)</label>
                                                <input
                                                    v-model.number="textConfig.timeout"
                                                    type="number"
                                                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm"
                                                    placeholder="300000"
                                                />
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                <!-- 图片生成模型配置 -->
                                <div class="bg-white border border-gray-200 rounded-lg p-4 md:p-6">
                                    <h3 class="text-lg font-semibold text-gray-800 flex items-center mb-4">
                                        <svg class="w-5 h-5 mr-3 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path
                                                stroke-linecap="round"
                                                stroke-linejoin="round"
                                                stroke-width="2"
                                                d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"
                                            ></path>
                                        </svg>
                                        图片生成模型配置
                                    </h3>

                                    <div class="space-y-4">
                                        <!-- API地址 -->
                                        <div>
                                            <label class="block text-sm font-medium text-gray-700 mb-2">API地址</label>
                                            <input
                                                v-model="imageConfig.baseUrl"
                                                type="text"
                                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500 text-sm"
                                                placeholder="https://api.example.com/v4/images/generations"
                                            />
                                            <p class="text-xs text-gray-500 mt-1">图片生成API的完整地址，包含具体的端点路径</p>
                                        </div>

                                        <!-- 配置项网格 -->
                                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                            <div>
                                                <label class="block text-sm font-medium text-gray-700 mb-2">API密钥</label>
                                                <input
                                                    v-model="imageConfig.apiKey"
                                                    type="password"
                                                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500 text-sm"
                                                    placeholder="输入API密钥"
                                                />
                                            </div>

                                            <div>
                                                <label class="block text-sm font-medium text-gray-700 mb-2">模型名称</label>
                                                <input
                                                    v-model="imageConfig.model"
                                                    type="text"
                                                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500 text-sm"
                                                    placeholder="cogview-3-flash"
                                                />
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <!-- PC端：右侧区域底部按钮 -->
                            <div class="hidden md:flex justify-end gap-3 px-6 py-4 border-t border-gray-200 bg-gray-50 flex-shrink-0">
                                <button @click="resetToDefault" class="px-4 py-2 text-gray-600 border border-gray-300 rounded hover:bg-gray-100 transition-colors text-sm">
                                    恢复默认
                                </button>
                                <button @click="saveSettings" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors text-sm">保存设置</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 移动端：固定底部按钮 -->
            <div class="md:hidden flex justify-end gap-3 px-4 py-3 border-t border-gray-200 bg-gray-50 flex-shrink-0">
                <button @click="resetToDefault" class="px-4 py-2 text-gray-600 border border-gray-300 rounded hover:bg-gray-100 transition-colors text-sm">恢复默认</button>
                <button @click="saveSettings" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors text-sm">保存设置</button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
import { useSettingsStore } from '../stores/settings'

const props = defineProps({
    isVisible: {
        type: Boolean,
        default: false
    }
})

const emit = defineEmits(['close', 'save'])

const settingsStore = useSettingsStore()

// 配置状态管理

// 轻提示状态
const toast = reactive({
    show: false,
    message: '',
    type: 'success' // 'success' | 'info'
})

// 显示提示
const showToast = (message, type = 'success') => {
    toast.message = message
    toast.type = type
    toast.show = true

    // 2秒后自动隐藏
    setTimeout(() => {
        toast.show = false
    }, 2000)
}

// 文本生成配置
const textConfig = reactive({
    baseUrl: '',
    apiKey: '',
    model: '',
    temperature: 0.7,
    timeout: 300000
})

// 图片生成配置
const imageConfig = reactive({
    baseUrl: '',
    apiKey: '',
    model: ''
})

// 监听弹窗显示状态，加载当前配置并控制背景滚动
watch(
    () => props.isVisible,
    visible => {
        if (visible) {
            loadCurrentSettings()
            // 阻止背景页面滚动
            document.body.style.overflow = 'hidden'
        } else {
            // 恢复背景页面滚动
            document.body.style.overflow = ''
        }
    }
)

// 加载当前设置
const loadCurrentSettings = () => {
    const settings = settingsStore.getSettings()

    // 加载文本生成配置
    textConfig.baseUrl = settings.textGeneration.baseUrl
    textConfig.apiKey = settings.textGeneration.apiKey
    textConfig.model = settings.textGeneration.model
    textConfig.temperature = settings.textGeneration.temperature
    textConfig.timeout = settings.textGeneration.timeout

    // 加载图片生成配置
    imageConfig.baseUrl = settings.imageGeneration.baseUrl
    imageConfig.apiKey = settings.imageGeneration.apiKey
    imageConfig.model = settings.imageGeneration.model
}

// 保存设置
const saveSettings = () => {
    const newSettings = {
        textGeneration: {
            baseUrl: textConfig.baseUrl,
            apiKey: textConfig.apiKey,
            model: textConfig.model,
            temperature: textConfig.temperature,
            timeout: textConfig.timeout
        },
        imageGeneration: {
            baseUrl: imageConfig.baseUrl,
            apiKey: imageConfig.apiKey,
            model: imageConfig.model
        }
    }

    settingsStore.updateSettings(newSettings)
    showToast('设置已保存', 'success')
    emit('save')

    // 延迟关闭弹窗，让用户看到提示
    setTimeout(() => {
        closeModal()
    }, 500)
}

// 恢复默认设置
const resetToDefault = () => {
    settingsStore.resetToDefault()
    loadCurrentSettings()
    showToast('已恢复默认设置', 'info')
}

// 关闭弹窗
const closeModal = () => {
    emit('close')
}
</script>
