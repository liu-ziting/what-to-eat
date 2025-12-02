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
                        <!-- 左侧：打赏作者区域 -->
                        <div
                            class="md:w-1/3 bg-gradient-to-br from-yellow-300 via-orange-400 to-pink-500 p-4 md:p-6 border-b md:border-b-0 md:border-r border-orange-300 md:flex-shrink-0 md:h-full relative overflow-hidden"
                        >
                            <!-- 背景装饰 -->
                            <div class="absolute top-0 right-0 w-32 h-32 bg-white/20 rounded-full blur-2xl -mr-16 -mt-16"></div>
                            <div class="absolute bottom-0 left-0 w-24 h-24 bg-yellow-200/30 rounded-full blur-xl -ml-12 -mb-12"></div>
                            <div class="absolute inset-0 bg-[url('/noise.png')] opacity-10 mix-blend-overlay"></div>

                            <div class="relative z-10 md:h-full md:flex md:flex-col md:justify-center items-center text-center">
                                <!-- Title -->
                                <div class="mb-6">
                                    <div class="inline-flex items-center gap-2 bg-white/90 backdrop-blur-sm text-orange-600 px-4 py-2 rounded-full shadow-lg transform hover:scale-105 transition-transform duration-300 border border-orange-200">
                                        <span class="text-xl animate-bounce">☕</span>
                                        <span class="font-bold">支持开发者</span>
                                    </div>
                                </div>

                                <!-- Description -->
                                <div class="mb-6 space-y-2">
                                    <p class="text-sm text-white font-bold drop-shadow-sm">🤖 AI需要成本，您的支持让项目走得更远</p>
                                    <span class="inline-block text-xs text-orange-900 bg-white/40 px-3 py-1 rounded-full backdrop-blur-sm border border-white/30 font-medium">每一份支持都是对开源精神的鼓励</span>
                                </div>

                                <!-- Image -->
                                <div class="mb-6 relative group">
                                    <div class="absolute -inset-1 bg-gradient-to-r from-yellow-300 to-pink-500 rounded-xl blur opacity-50 group-hover:opacity-75 transition duration-1000 group-hover:duration-200"></div>
                                    <img
                                        src="/wx.jpg"
                                        alt="微信收款码"
                                        class="relative w-40 h-40 md:w-48 md:h-48 rounded-xl shadow-2xl object-cover mx-auto transform transition-transform duration-300 group-hover:scale-105 ring-4 ring-white/30"
                                    />
                                </div>

                                <!-- Features List -->
                                <div class="space-y-2 text-left bg-white/20 p-4 rounded-xl w-full max-w-xs mx-auto backdrop-blur-sm border border-white/30 shadow-inner">
                                    <div class="flex items-center gap-3">
                                        <div class="w-5 h-5 rounded-full bg-white/80 flex items-center justify-center flex-shrink-0 shadow-sm">
                                            <span class="text-orange-600 text-xs font-bold">✓</span>
                                        </div>
                                        <span class="text-sm text-white font-medium drop-shadow-sm">维持AI服务运行</span>
                                    </div>
                                    <div class="flex items-center gap-3">
                                        <div class="w-5 h-5 rounded-full bg-white/80 flex items-center justify-center flex-shrink-0 shadow-sm">
                                            <span class="text-orange-600 text-xs font-bold">✓</span>
                                        </div>
                                        <span class="text-sm text-white font-medium drop-shadow-sm">持续功能更新</span>
                                    </div>
                                    <div class="flex items-center gap-3">
                                        <div class="w-5 h-5 rounded-full bg-white/80 flex items-center justify-center flex-shrink-0 shadow-sm">
                                            <span class="text-orange-600 text-xs font-bold">✓</span>
                                        </div>
                                        <span class="text-sm text-white font-medium drop-shadow-sm">开源项目维护</span>
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
