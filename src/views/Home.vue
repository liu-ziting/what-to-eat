<template>
    <div class="min-h-screen bg-yellow-400 px-2 md:px-4 py-6">
        <!-- 全局导航 -->
        <GlobalNavigation />

        <div class="max-w-7xl mx-auto">
            <!-- 步骤1: 输入食材 -->
            <div class="mb-6">
                <div class="bg-pink-400 text-white px-4 py-2 rounded-t-lg border-2 border-[#0A0910] border-b-0 inline-block">
                    <span class="font-bold">1. 输入食材</span>
                </div>
                <div class="bg-white border-2 border-[#0A0910] rounded-lg rounded-tl-none p-4 md:p-6 md:pb-10">
                    <div class="text-center mb-6">
                        <div class="w-16 h-16 bg-black rounded-lg flex items-center justify-center mx-auto mb-4">
                            <span class="text-white text-2xl">🥬</span>
                        </div>
                        <h2 class="text-2xl font-bold text-dark-800 mb-2">添加食材</h2>
                        <p class="text-gray-600">输入你现有的食材，按回车添加</p>
                        <p class="text-xs text-gray-500 mt-1">支持蔬菜、肉类、调料等 (最多10种)</p>
                    </div>

                    <!-- 食材输入区域 -->
                    <div class="space-y-4">
                        <!-- 已添加的食材 -->
                        <div v-if="ingredients.length > 0" class="flex flex-wrap gap-2">
                            <div
                                v-for="ingredient in ingredients"
                                :key="ingredient"
                                class="inline-flex items-center gap-2 bg-yellow-400 text-dark-800 px-3 py-2 rounded-full text-sm font-medium border-2 border-[#0A0910]"
                            >
                                {{ ingredient }}
                                <button @click="removeIngredient(ingredient)" class="hover:bg-yellow-500 rounded-full p-1 transition-colors">
                                    <span class="text-xs">✕</span>
                                </button>
                            </div>
                        </div>

                        <!-- 输入框 -->
                        <div class="flex gap-2">
                            <input
                                v-model="currentIngredient"
                                @keyup.enter="addIngredient"
                                placeholder="输入食材名称，按回车添加..."
                                class="flex-1 p-3 md:p-4 border-2 border-[#0A0910] rounded-lg text-sm md:text-lg font-medium focus:outline-none focus:ring-2 focus:ring-pink-400"
                            />
                            <div class="relative group">
                                <button
                                    @click="triggerImageUpload"
                                    :disabled="isRecognizing"
                                    class="relative h-full px-3 bg-white hover:bg-gray-50 disabled:bg-gray-100 rounded-lg border-2 border-[#0A0910] transition-all duration-200 disabled:cursor-not-allowed flex items-center justify-center min-w-[3rem]"
                                >
                                    <!-- 正常状态 -->
                                    <span v-if="!isRecognizing" class="text-2xl" style="margin-top: -8px">📷</span>

                                    <!-- 加载状态 -->
                                    <div v-else class="relative flex items-center justify-center">
                                        <div class="absolute w-5 h-5 border-2 border-gray-300 rounded-full"></div>
                                        <div class="absolute w-5 h-5 border-2 border-pink-500 border-t-transparent rounded-full animate-spin"></div>
                                    </div>
                                </button>

                                <!-- 提示文字 -->
                                <div
                                    class="absolute bottom-full left-1/2 -translate-x-1/2 mb-2 opacity-0 group-hover:opacity-100 transition-opacity duration-200 pointer-events-none whitespace-nowrap"
                                >
                                    <div class="bg-gray-900 text-white text-xs px-3 py-2 rounded-lg shadow-xl">
                                        拍照识别
                                        <div class="absolute top-full left-1/2 -translate-x-1/2 -mt-1">
                                            <div class="w-2 h-2 bg-gray-900 transform rotate-45"></div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <input ref="imageInput" type="file" accept="image/*" @change="handleImageUpload" class="hidden" />
                        </div>

                        <!-- 快速选择食材 -->
                        <div class="mt-4">
                            <button
                                @click="toggleIngredientPicker"
                                class="flex items-center justify-between w-full p-2 text-sm text-gray-700 hover:text-gray-900 hover:bg-gray-50 rounded-lg border border-gray-200 hover:border-gray-300 transition-all duration-200"
                            >
                                <span class="flex items-center gap-2">
                                    <span class="text-base">🥬</span>
                                    <span class="font-medium">快速选择食材</span>
                                </span>
                                <span class="transform transition-transform duration-200 text-gray-400" :class="{ 'rotate-180': showIngredientPicker }">
                                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
                                    </svg>
                                </span>
                            </button>

                            <div v-if="showIngredientPicker" class="mt-2 border border-gray-200 rounded-lg overflow-hidden bg-white shadow-sm">
                                <!-- 食材展示区域 -->
                                <div class="p-3 max-h-80 overflow-y-auto">
                                    <div class="space-y-4">
                                        <div v-for="category in ingredientCategories" :key="category.id">
                                            <!-- 分类标题 -->
                                            <div class="flex items-center gap-2 mb-2">
                                                <span class="text-sm">{{ category.icon }}</span>
                                                <span class="text-sm font-bold text-gray-700">{{ category.name }}</span>
                                                <div class="flex-1 h-px bg-gray-200"></div>
                                            </div>

                                            <!-- 食材按钮 -->
                                            <div class="flex flex-wrap gap-1.5">
                                                <button
                                                    v-for="item in category.items"
                                                    :key="item"
                                                    @click="quickAddIngredient(item)"
                                                    :disabled="ingredients.includes(item) || ingredients.length >= 10"
                                                    class="px-3 py-1.5 text-xs font-medium rounded-full border border-gray-300 hover:border-pink-400 hover:bg-pink-50 disabled:bg-gray-100 disabled:text-gray-400 disabled:cursor-not-allowed disabled:border-gray-200 transition-all duration-200 hover:shadow-sm"
                                                    :class="{
                                                        'bg-yellow-100 border-yellow-400 text-yellow-800 shadow-sm': ingredients.includes(item),
                                                        'hover:scale-105': !ingredients.includes(item) && ingredients.length < 10
                                                    }"
                                                >
                                                    {{ item }}
                                                </button>
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                <!-- 底部状态栏 -->
                                <div class="px-3 py-2 bg-gray-50 border-t border-gray-200 text-xs text-gray-500 flex justify-between items-center">
                                    <span>点击食材快速添加到列表</span>
                                    <span class="font-medium">已选择 {{ ingredients.length }}/10</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 步骤2和3: 左右布局 -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <!-- 步骤2: 选择菜系 -->
                <div>
                    <div class="bg-green-400 text-white px-4 py-2 rounded-t-lg border-2 border-[#0A0910] border-b-0 inline-block">
                        <span class="font-bold">2. 选择菜系</span>
                    </div>
                    <div class="bg-white border-2 border-[#0A0910] rounded-lg rounded-tl-none p-4 md:p-6 h-full">
                        <div>
                            <!-- 自定义要求提示 -->
                            <div v-if="customPrompt.trim()" class="mb-4 p-3 bg-blue-50 border-2 border-blue-200 rounded-lg text-center">
                                <p class="text-sm text-blue-700 mb-2">✓ 已设置自定义要求，将优先使用自定义要求生成菜谱</p>
                                <button @click="clearCustomPrompt" class="text-blue-600 hover:text-blue-700 underline text-sm">清除自定义要求以选择菜系</button>
                            </div>

                            <!-- 中华八大菜系 -->
                            <div class="mb-4" :class="{ 'opacity-50': customPrompt.trim() }">
                                <h5 class="text-xs font-bold text-gray-700 mb-2 flex items-center gap-1">🇨🇳 中华八大菜系</h5>
                                <div class="grid grid-cols-3 gap-2">
                                    <button
                                        v-for="cuisine in cuisines.slice(0, 8)"
                                        :key="cuisine.id"
                                        @click="selectCuisine(cuisine)"
                                        :class="[
                                            'p-2 rounded-lg border-2 border-[#0A0910] font-medium text-sm transition-all duration-200 relative flex items-center justify-center gap-1',
                                            selectedCuisines.includes(cuisine.id) ? 'bg-yellow-400 text-dark-800' : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                                        ]"
                                    >
                                        <span>{{ cuisine.avatar }}</span>
                                        <span>{{ cuisine.name.replace('大师', '') }}</span>
                                    </button>
                                </div>
                            </div>

                            <!-- 国际菜系 -->
                            <div class="mb-6" :class="{ 'opacity-50': customPrompt.trim() }">
                                <h5 class="text-xs font-bold text-gray-700 mb-2 flex items-center gap-1">🌍 国际菜系</h5>
                                <div class="grid grid-cols-3 gap-2">
                                    <button
                                        v-for="cuisine in cuisines.slice(8)"
                                        :key="cuisine.id"
                                        @click="selectCuisine(cuisine)"
                                        :class="[
                                            'p-2 rounded-lg border-2 border-[#0A0910] font-medium text-sm transition-all duration-200 relative flex items-center justify-center gap-1',
                                            selectedCuisines.includes(cuisine.id) ? 'bg-yellow-400 text-dark-800' : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                                        ]"
                                    >
                                        <span>{{ cuisine.avatar }}</span>
                                        <span>{{ cuisine.name.replace('料理大师', '').replace('大师', '') }}</span>
                                    </button>
                                </div>
                            </div>

                            <!-- 或自定义要求 -->
                            <div class="border-t border-gray-200 pt-4">
                                <!-- 折叠按钮 -->
                                <button
                                    @click="showCustomPrompt = !showCustomPrompt"
                                    class="flex items-center justify-between w-full p-3 text-sm text-gray-700 hover:text-gray-900 hover:bg-blue-50 rounded-lg border-2 border-blue-200 hover:border-blue-300 transition-all duration-200 mb-3"
                                    :class="{ 'bg-blue-50 border-blue-300': showCustomPrompt || customPrompt.trim() }"
                                >
                                    <span class="flex items-center gap-2">
                                        <span class="text-base">💭</span>
                                        <span class="font-medium">或自定义要求</span>
                                        <span v-if="customPrompt.trim()" class="text-xs bg-blue-500 text-white px-2 py-1 rounded-full">已设置</span>
                                    </span>
                                    <span class="transform transition-transform duration-200 text-gray-400" :class="{ 'rotate-180': showCustomPrompt }">
                                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
                                        </svg>
                                    </span>
                                </button>

                                <!-- 自定义要求内容区域 -->
                                <div v-if="showCustomPrompt" class="bg-blue-100 border-2 border-blue-300 rounded-lg p-3">
                                    <!-- 快速预设选项 -->
                                    <div class="mb-3">
                                        <button
                                            @click="togglePresetPicker"
                                            class="flex items-center justify-between w-full p-2 text-sm text-gray-700 hover:text-gray-900 hover:bg-white/50 rounded-lg border border-blue-200 hover:border-blue-300 transition-all duration-200 mb-2"
                                        >
                                            <span class="flex items-center gap-2">
                                                <span class="text-base">⚡</span>
                                                <span class="font-medium">快速预设</span>
                                            </span>
                                            <span class="transform transition-transform duration-200 text-gray-400" :class="{ 'rotate-180': showPresetPicker }">
                                                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
                                                </svg>
                                            </span>
                                        </button>

                                        <div v-if="showPresetPicker" class="space-y-2 mb-3 p-2 bg-white/70 rounded-lg border border-blue-200 shadow-sm max-h-40 overflow-y-auto">
                                            <!-- 场景预设 -->
                                            <div>
                                                <h6 class="text-xs font-medium text-gray-700 mb-1 flex items-center gap-1">🎯 场景需求</h6>
                                                <div class="flex flex-wrap gap-1">
                                                    <button
                                                        v-for="preset in scenePresets"
                                                        :key="preset.id"
                                                        @click="applyPreset(preset.prompt)"
                                                        class="px-2 py-1 text-xs font-medium rounded-full border border-blue-300 hover:border-blue-400 hover:bg-blue-50 hover:text-blue-700 transition-all duration-200"
                                                    >
                                                        {{ preset.name }}
                                                    </button>
                                                </div>
                                            </div>

                                            <!-- 口味偏好 -->
                                            <div>
                                                <h6 class="text-xs font-medium text-gray-700 mb-1 flex items-center gap-1">👅 口味偏好</h6>
                                                <div class="flex flex-wrap gap-1">
                                                    <button
                                                        v-for="preset in tastePresets"
                                                        :key="preset.id"
                                                        @click="applyPreset(preset.prompt)"
                                                        class="px-2 py-1 text-xs font-medium rounded-full border border-green-300 hover:border-green-400 hover:bg-green-50 hover:text-green-700 transition-all duration-200"
                                                    >
                                                        {{ preset.name }}
                                                    </button>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <!-- 自定义输入区域 -->
                                    <div>
                                        <label class="block text-sm font-bold text-blue-800 mb-2">自由描述：</label>
                                        <textarea
                                            v-model="customPrompt"
                                            @input="limitCustomPrompt"
                                            placeholder="例如：做一道清淡的汤，适合老人食用，不要太咸..."
                                            class="w-full p-2 border-2 border-blue-300 rounded-lg text-sm resize-none focus:outline-none focus:border-blue-500 h-20"
                                            maxlength="200"
                                        ></textarea>
                                        <div v-if="customPrompt.trim()" class="mt-1 flex justify-between items-center">
                                            <span class="text-xs text-green-600">✓ 已设置自定义要求</span>
                                            <button @click="customPrompt = ''" class="text-xs text-red-600 hover:text-red-700 underline">清除</button>
                                        </div>
                                    </div>

                                    <!-- 随机灵感 -->
                                    <div class="mt-2">
                                        <button
                                            @click="getRandomInspiration"
                                            class="w-full py-1.5 px-2 bg-gradient-to-r from-purple-400 to-pink-400 hover:from-purple-500 hover:to-pink-500 text-white text-sm font-medium rounded-lg border-2 border-[#0A0910] transition-all duration-200 transform"
                                        >
                                            ✨ 随机灵感
                                        </button>
                                    </div>

                                    <!-- 底部提示 -->
                                    <div class="mt-2 pt-2 border-t border-blue-200">
                                        <div class="flex items-center justify-between text-xs text-blue-600">
                                            <span>💡 提示：越具体越好！</span>
                                            <span :class="{ 'text-red-500': customPrompt.length > 180 }">{{ customPrompt.length }}/200</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- 步骤3: 交给大师 -->
                <div class="max-sm:mt-10">
                    <div class="bg-orange-400 text-white px-4 py-2 rounded-t-lg border-2 border-[#0A0910] border-b-0 inline-block">
                        <span class="font-bold">3. 交给大师</span>
                    </div>
                    <div class="bg-white border-2 border-[#0A0910] rounded-lg rounded-tl-none p-4 md:p-6 h-full">
                        <div class="text-center h-full flex flex-col">
                            <div class="w-16 h-16 bg-gradient-to-br from-orange-400 to-red-500 rounded-lg flex items-center justify-center mx-auto mb-4">
                                <span class="text-white text-2xl">👨‍🍳</span>
                            </div>
                            <h2 class="text-xl font-bold text-dark-800 mb-2">准备开始烹饪</h2>
                            <p class="text-gray-600 mb-4 text-sm">大师已准备就绪，点击按钮开始创作美味佳肴</p>

                            <!-- 当前配置预览 -->
                            <div class="bg-gray-50 rounded-lg p-3 mb-4 text-left flex-1">
                                <h3 class="font-bold text-sm text-gray-700 mb-2 flex items-center gap-2">
                                    <span>📋</span>
                                    <span>当前配置</span>
                                </h3>

                                <!-- 食材列表 -->
                                <div class="mb-2">
                                    <span class="text-xs font-medium text-gray-600">食材 ({{ ingredients.length }})：</span>
                                    <div v-if="ingredients.length > 0" class="flex flex-wrap gap-1 mt-1">
                                        <span v-for="ingredient in ingredients" :key="ingredient" class="inline-block bg-yellow-200 text-yellow-800 px-2 py-1 rounded text-xs">
                                            {{ ingredient }}
                                        </span>
                                    </div>
                                    <span v-else class="text-xs text-gray-400">未添加食材</span>
                                </div>

                                <!-- 菜系和大师选择 -->
                                <div class="mb-2">
                                    <span class="text-xs font-medium text-gray-600">菜系大师 ({{ selectedCuisines.length }})：</span>
                                    <div v-if="selectedCuisines.length > 0 && !customPrompt.trim()" class="mt-1">
                                        <div
                                            v-for="cuisineId in selectedCuisines"
                                            :key="cuisineId"
                                            class="inline-flex items-center gap-1 bg-green-200 text-green-800 px-2 py-1 rounded text-xs mr-1 mb-1"
                                        >
                                            <span>{{ cuisines.find(c => c.id === cuisineId)?.avatar || '👨‍🍳' }}</span>
                                            <span>{{ cuisines.find(c => c.id === cuisineId)?.name }}</span>
                                        </div>
                                    </div>
                                    <span v-else-if="!customPrompt.trim()" class="text-xs text-gray-400">未选择大师</span>
                                    <span v-else class="text-xs text-blue-600">使用自定义要求</span>
                                </div>

                                <!-- 自定义要求 -->
                                <div v-if="customPrompt.trim()">
                                    <span class="text-xs font-medium text-gray-600">自定义要求：</span>
                                    <p class="text-xs text-blue-700 mt-1 bg-blue-50 p-2 rounded">
                                        {{ customPrompt.length > 50 ? customPrompt.substring(0, 50) + '...' : customPrompt }}
                                    </p>
                                </div>
                            </div>

                            <!-- 生成按钮 -->
                            <button
                                @click="generateRecipes"
                                :disabled="ingredients.length === 0 || isLoading"
                                class="w-full bg-gradient-to-r from-orange-500 to-red-500 hover:from-orange-600 hover:to-red-600 disabled:from-gray-400 disabled:to-gray-400 text-white px-6 py-3 rounded-lg font-bold text-base md:text-lg border-2 border-[#0A0910] transition-all duration-300 transform disabled:scale-100 disabled:cursor-not-allowed shadow-lg mb-3"
                            >
                                <span class="flex items-center gap-2 justify-center">
                                    <template v-if="isLoading">
                                        <div class="animate-spin w-5 h-5 border-2 border-white border-t-transparent rounded-full"></div>
                                        <span v-if="recipes.length === 0">生成中...</span>
                                        <span v-else>{{ loadingText }}</span>
                                    </template>
                                    <template v-else>
                                        <span class="text-xl">✨</span>
                                        <span>{{ customPrompt.trim() ? '按要求生成' : '交给大师' }}</span>
                                    </template>
                                </span>
                            </button>

                            <!-- 提示信息 -->
                            <div class="text-sm">
                                <p v-if="customPrompt.trim()" class="text-blue-600">🎯 将根据您的自定义要求生成菜谱</p>
                                <p v-else-if="selectedCuisines.length > 0" class="text-green-600">🍽️ 将生成 {{ selectedCuisines.length }} 个菜系的菜谱</p>
                                <p class="text-xs text-gray-500 mt-1">大师将为您精心设计菜谱流程</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 步骤4: 菜谱结果 -->
            <div ref="resultsSection" class="mt-16">
                <div class="bg-dark-800 text-white px-4 py-2 rounded-t-lg border-2 border-[#0A0910] border-b-0 inline-block">
                    <span class="font-bold">4. 菜谱结果</span>
                </div>
                <div class="bg-white border-2 border-[#0A0910] rounded-lg rounded-tl-none p-2 md:p-6">
                    <!-- 移除这个整体加载状态，因为我们现在使用菜系模块加载 -->

                    <!-- 流式加载状态 - 显示菜系模块和加载状态 -->
                    <div v-if="isLoading || cuisineSlots.length > 0">
                        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                            <!-- 遍历所有要生成的菜系，显示对应的卡片 -->
                            <div
                                v-for="(cuisineInfo, index) in cuisineSlots"
                                :key="cuisineInfo.id"
                                class="border-2 border-[#0A0910] rounded-lg overflow-hidden"
                                :class="cuisineInfo.recipe ? 'animate-fade-in-up' : ''"
                                :style="cuisineInfo.recipe ? { animationDelay: `${index * 0.2}s` } : {}"
                            >
                                <!-- 如果菜谱已生成，显示菜谱卡片 -->
                                <RecipeCard v-if="cuisineInfo.recipe" :recipe="cuisineInfo.recipe" />

                                <!-- 如果菜谱生成失败，显示友好错误信息 -->
                                <div v-else-if="cuisineInfo.error" class="bg-white error-card">
                                    <!-- 错误头部 -->
                                    <div class="bg-gradient-to-r from-red-400 to-orange-400 text-white p-4 md:p-6 border-b-2 border-black">
                                        <div class="flex items-center justify-between">
                                            <div class="flex-1">
                                                <h3 class="text-lg font-bold mb-1 flex items-center gap-2">
                                                    <span class="animate-bounce">😅</span>
                                                    {{ cuisineInfo.name }}不会这道菜，哈哈
                                                </h3>
                                                <div class="flex items-center gap-3 text-sm">
                                                    <span class="bg-white/20 px-2 py-1 rounded text-xs">{{ cuisineInfo.name }}</span>
                                                    <span class="flex items-center gap-1">
                                                        <span>😓</span>
                                                        技能点不够
                                                    </span>
                                                    <span>🎯 开小差了</span>
                                                </div>
                                            </div>
                                            <div class="text-2xl ml-2">🤷‍♂️</div>
                                        </div>
                                    </div>

                                    <!-- 错误内容区域 -->
                                    <div class="p-4 md:p-6 text-center">
                                        <div class="mb-4">
                                            <div class="w-16 h-16 bg-orange-100 rounded-lg flex items-center justify-center mx-auto mb-4">
                                                <span class="text-orange-500 text-2xl">🤔</span>
                                            </div>
                                            <h4 class="text-lg font-bold text-gray-800 mb-2">大师表示很为难</h4>
                                            <p class="text-gray-600 text-sm mb-4">{{ cuisineInfo.name }}看了看你的食材，挠了挠头说："这个组合我还没学会呢！"</p>
                                        </div>

                                        <!-- 建议区域 -->
                                        <div class="bg-yellow-50 border-2 border-yellow-200 rounded-lg p-4 mb-4">
                                            <h5 class="text-sm font-bold text-yellow-800 mb-2 flex items-center gap-1 justify-center">💡 大师的建议</h5>
                                            <div class="text-xs text-yellow-700 space-y-1">
                                                <p>• 试试其他菜系大师，他们可能有不同的想法</p>
                                                <p>• 调整一下食材搭配，或许会有惊喜</p>
                                                <p>• 使用自定义要求，给大师一些灵感</p>
                                            </div>
                                        </div>

                                        <!-- 重试按钮 -->
                                        <button
                                            @click="retryFailedCuisine(cuisineInfo)"
                                            class="bg-gradient-to-r from-orange-500 to-red-500 hover:from-orange-600 hover:to-red-600 text-white px-4 py-2 rounded-lg font-medium text-sm border-2 border-[#0A0910] transition-all duration-200 transform hover:scale-105"
                                        >
                                            🔄 再试一次
                                        </button>
                                    </div>
                                </div>

                                <!-- 如果菜谱还在生成中，显示加载状态 -->
                                <div v-else class="bg-white loading-card">
                                    <!-- 菜系头部 -->
                                    <div class="bg-gradient-to-r from-gray-400 to-gray-500 text-white p-4 md:p-6 border-b-2 border-black">
                                        <div class="flex items-center justify-between">
                                            <div class="flex-1">
                                                <h3 class="text-lg font-bold mb-1 flex items-center gap-2">
                                                    <span class="animate-pulse">👨‍🍳</span>
                                                    {{ cuisineInfo.name }}创作中...
                                                </h3>
                                                <!-- 移动端：垂直布局 -->
                                                <div class="flex flex-col gap-1 md:hidden">
                                                    <div class="flex items-center gap-2">
                                                        <span class="bg-white/20 px-2 py-1 rounded text-xs animate-pulse">{{ cuisineInfo.name }}</span>
                                                        <span class="text-xs">📊 精心制作</span>
                                                    </div>
                                                    <div class="flex items-center gap-1 text-xs">
                                                        <span class="animate-spin">⏱️</span>
                                                        <span>预计10-20秒</span>
                                                    </div>
                                                </div>
                                                <!-- 桌面端：水平布局 -->
                                                <div class="hidden md:flex items-center gap-3 text-sm">
                                                    <span class="bg-white/20 px-2 py-1 rounded text-xs animate-pulse">{{ cuisineInfo.name }}</span>
                                                    <span class="flex items-center gap-1">
                                                        <span class="animate-spin">⏱️</span>
                                                        预计10-20秒
                                                    </span>
                                                    <span>📊 精心制作</span>
                                                </div>
                                            </div>
                                            <div class="text-2xl ml-2 animate-bounce">⏳</div>
                                        </div>
                                    </div>

                                    <!-- 加载内容区域 -->
                                    <div class="p-4 md:p-6">
                                        <!-- 食材预览 -->
                                        <div class="mb-4">
                                            <h4 class="text-sm font-bold text-dark-800 mb-2 flex items-center gap-1">🥬 使用食材</h4>
                                            <div class="flex flex-wrap gap-1">
                                                <span
                                                    v-for="ingredient in ingredients"
                                                    :key="ingredient"
                                                    class="bg-yellow-200 text-yellow-800 px-2 py-1 rounded text-xs font-medium border border-yellow-400 animate-pulse"
                                                >
                                                    {{ ingredient }}
                                                </span>
                                            </div>
                                        </div>

                                        <!-- 步骤预览骨架 -->
                                        <div class="mb-4">
                                            <h4 class="text-sm font-bold text-dark-800 mb-2 flex items-center gap-1">📝 制作步骤</h4>
                                            <div class="space-y-2">
                                                <div v-for="i in 3" :key="i" class="flex gap-2 p-2 bg-gray-50 rounded border border-gray-200">
                                                    <div class="flex-shrink-0 w-5 h-5 bg-gray-300 rounded shimmer-effect"></div>
                                                    <div class="flex-1 space-y-1">
                                                        <div class="h-3 bg-gray-300 rounded shimmer-effect" :style="{ width: 60 + Math.random() * 30 + '%' }"></div>
                                                        <div class="h-2 bg-gray-200 rounded shimmer-effect" :style="{ width: 40 + Math.random() * 20 + '%' }"></div>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>

                                        <!-- 生成状态 -->
                                        <div class="text-center py-6 bg-gradient-to-br from-orange-50 to-yellow-50 rounded-lg border-2 border-dashed border-orange-200">
                                            <div class="w-16 h-16 border-4 border-orange-200 border-t-orange-500 rounded-full animate-spin mx-auto mb-4"></div>
                                            <h3 class="text-lg font-bold text-dark-800 mb-2">{{ cuisineInfo.name }}正在创作中...</h3>
                                            <p class="text-gray-600 text-sm mb-3">{{ cuisineInfo.loadingText || loadingText }}</p>

                                            <!-- 进度条 -->
                                            <div class="max-w-xs mx-auto">
                                                <div class="bg-gray-200 rounded-full h-3 overflow-hidden">
                                                    <div
                                                        class="bg-gradient-to-r from-orange-400 to-yellow-500 h-3 rounded-full transition-all duration-1000 relative"
                                                        :style="{ width: cuisineInfo.progress + '%' }"
                                                    >
                                                        <div class="absolute inset-0 bg-white/30 animate-pulse"></div>
                                                    </div>
                                                </div>
                                                <p class="text-xs text-gray-500 mt-2">{{ Math.round(cuisineInfo.progress) }}% 完成</p>
                                            </div>

                                            <div class="mt-4 flex justify-center items-center gap-1 text-xs text-gray-500">
                                                <span class="animate-bounce" style="animation-delay: 0s">●</span>
                                                <span class="animate-bounce" style="animation-delay: 0.2s">●</span>
                                                <span class="animate-bounce" style="animation-delay: 0.4s">●</span>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 错误状态 -->
                    <div v-else-if="errorMessage" class="text-center py-12">
                        <div class="w-16 h-16 bg-red-100 rounded-lg flex items-center justify-center mx-auto mb-4">
                            <span class="text-red-500 text-2xl">⚠️</span>
                        </div>
                        <h3 class="text-xl font-bold text-red-600 mb-2">生成失败</h3>
                        <p class="text-red-500 mb-4">{{ errorMessage }}</p>
                        <button
                            @click="generateRecipes"
                            :disabled="ingredients.length === 0"
                            class="bg-red-500 hover:bg-red-600 disabled:bg-gray-400 text-white px-6 py-2 rounded-lg font-medium border-2 border-[#0A0910] transition-all duration-200 disabled:cursor-not-allowed"
                        >
                            🔄 重新生成
                        </button>
                    </div>

                    <!-- 空状态 -->
                    <div v-else-if="recipes.length === 0" class="text-center py-12">
                        <div class="w-16 h-16 bg-gray-200 rounded-lg flex items-center justify-center mx-auto mb-4">
                            <span class="text-gray-400 text-2xl">⭐</span>
                        </div>
                        <h3 class="text-xl font-bold text-gray-400 mb-2">等待魔法发生...</h3>
                        <p class="text-gray-500">添加食材并选择菜系开始创作</p>
                    </div>

                    <!-- 传统菜谱结果显示 - 只在没有使用槽位系统时显示 -->
                    <div v-else-if="recipes.length > 0 && cuisineSlots.length === 0" class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                        <div
                            v-for="(recipe, index) in recipes"
                            :key="recipe.id"
                            class="border-2 border-[#0A0910] rounded-lg overflow-hidden animate-fade-in-up"
                            :style="{ animationDelay: `${index * 0.2}s` }"
                        >
                            <RecipeCard :recipe="recipe" />
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 底部 -->
        <GlobalFooter />
    </div>
</template>

<style scoped>
@keyframes fade-in-up {
    from {
        opacity: 0;
        transform: translateY(20px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

@keyframes pulse-glow {
    0%,
    100% {
        box-shadow: 0 0 5px rgba(249, 115, 22, 0.3);
    }
    50% {
        box-shadow: 0 0 20px rgba(249, 115, 22, 0.6);
    }
}

@keyframes shimmer {
    0% {
        background-position: -200px 0;
    }
    100% {
        background-position: calc(200px + 100%) 0;
    }
}

.animate-fade-in-up {
    animation: fade-in-up 0.6s ease-out forwards;
    opacity: 0;
}

.loading-card {
    animation: pulse-glow 2s ease-in-out infinite;
}

.shimmer-effect {
    background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
    background-size: 200px 100%;
    animation: shimmer 1.5s infinite;
}
</style>

<script setup lang="ts">
import { ref, onUnmounted } from 'vue'
import { cuisines } from '@/config/cuisines'
import { ingredientCategories } from '@/config/ingredients'
import RecipeCard from '@/components/RecipeCard.vue'
import GlobalNavigation from '@/components/GlobalNavigation.vue'
import GlobalFooter from '@/components/GlobalFooter.vue'
import { generateCustomRecipe, generateMultipleRecipesStream, generateRecipe } from '@/services/aiService'
import type { Recipe, CuisineType } from '@/types'

// 响应式数据
const ingredients = ref<string[]>([])
const currentIngredient = ref('')
const selectedCuisines = ref<string[]>([])
const customPrompt = ref('')
const recipes = ref<Recipe[]>([])
const isLoading = ref(false)
const loadingText = ref('大师正在挑选食材...')
const resultsSection = ref<HTMLElement | null>(null)
const errorMessage = ref('')
const showIngredientPicker = ref(false)
const showPresetPicker = ref(false)
const showCustomPrompt = ref(false)

// 菜系槽位数据 - 用于显示加载状态和完成状态
interface CuisineSlot {
    id: string
    name: string
    recipe?: Recipe
    loadingText: string
    progress: number
    error?: boolean
    errorMessage?: string
}
const cuisineSlots = ref<CuisineSlot[]>([])

// 加载文字轮播 - 暂时未使用
// const loadingTexts = [
//     '大师正在挑选食材...',
//     '大师正在起火热锅...',
//     '大师正在爆香配料...',
//     '大师正在调制秘制酱料...',
//     '大师正在掌控火候...',
//     '大师正在精心摆盘...',
//     '美味佳肴即将出炉...'
// ]

let loadingInterval: NodeJS.Timeout | null = null

// 预设选项数据
const scenePresets = [
    { id: 'family', name: '家庭聚餐', prompt: '适合全家人一起享用的家常菜，老少皆宜，营养均衡' },
    { id: 'romantic', name: '浪漫晚餐', prompt: '适合情侣约会的精致菜品，卖相精美，氛围浪漫' },
    { id: 'quick', name: '快手菜', prompt: '制作简单快速的菜品，15分钟内完成，适合忙碌时光' },
    { id: 'party', name: '聚会小食', prompt: '适合朋友聚会的下酒菜或小食，方便分享' },
    { id: 'breakfast', name: '营养早餐', prompt: '健康营养的早餐搭配，提供一天的活力' },
    { id: 'lunch', name: '工作午餐', prompt: '适合上班族的午餐，营养丰富且不油腻' }
]

const tastePresets = [
    { id: 'light', name: '清淡口味', prompt: '口味清淡，少油少盐，突出食材本味' },
    { id: 'spicy', name: '香辣过瘾', prompt: '香辣刺激，层次丰富，适合喜欢辣味的人' },
    { id: 'sweet', name: '微甜可口', prompt: '带有淡淡甜味，口感温和，老少皆宜' },
    { id: 'sour', name: '酸爽开胃', prompt: '酸味突出，开胃解腻，适合夏天享用' },
    { id: 'savory', name: '鲜香浓郁', prompt: '鲜味十足，香气浓郁，回味无穷' },
    { id: 'crispy', name: '酥脆爽口', prompt: '口感酥脆，层次分明，嚼劲十足' }
]

// 健康偏好预设 - 暂时未使用
// const healthPresets = [
//     { id: 'lowfat', name: '低脂健康', prompt: '低脂肪制作，健康营养，适合减脂期间食用' },
//     { id: 'highprotein', name: '高蛋白', prompt: '富含优质蛋白质，适合健身人群和成长期儿童' },
//     { id: 'vegetarian', name: '素食主义', prompt: '纯素食制作，不含任何动物性食材，营养均衡' },
//     { id: 'diabetic', name: '控糖友好', prompt: '低糖低GI，适合糖尿病患者或需要控制血糖的人群' },
//     { id: 'elderly', name: '老人友好', prompt: '软烂易消化，营养丰富，适合老年人食用' },
//     { id: 'children', name: '儿童喜爱', prompt: '造型可爱，营养全面，适合儿童的口味偏好' }
// ]

// 烹饪方式预设 - 暂时未使用
// const cookingPresets = [
//     { id: 'steam', name: '清蒸', prompt: '采用蒸制方法，保持食材原味和营养' },
//     { id: 'stirfry', name: '爆炒', prompt: '大火爆炒，锁住食材鲜味，口感脆嫩' },
//     { id: 'braise', name: '红烧', prompt: '红烧制作，色泽红亮，味道浓郁' },
//     { id: 'soup', name: '煲汤', prompt: '制作成汤品，清香鲜美，营养丰富' },
//     { id: 'cold', name: '凉拌', prompt: '凉拌制作，清爽开胃，适合夏季' },
//     { id: 'grill', name: '烧烤', prompt: '烧烤方式制作，香气四溢，口感独特' }
// ]

// 添加食材
const addIngredient = () => {
    const ingredient = currentIngredient.value.trim()
    if (ingredient && !ingredients.value.includes(ingredient) && ingredients.value.length < 10) {
        ingredients.value.push(ingredient)
        currentIngredient.value = ''
    }
}

// 移除食材
const removeIngredient = (ingredient: string) => {
    const index = ingredients.value.indexOf(ingredient)
    if (index > -1) {
        ingredients.value.splice(index, 1)
    }
}

// 快速添加食材
const quickAddIngredient = (ingredient: string) => {
    if (!ingredients.value.includes(ingredient) && ingredients.value.length < 10) {
        ingredients.value.push(ingredient)
    }
}

// 图片识别相关
const imageInput = ref<HTMLInputElement | null>(null)
const isRecognizing = ref(false)

// 触发图片上传
const triggerImageUpload = () => {
    imageInput.value?.click()
}

// 处理图片上传和识别
const handleImageUpload = async (event: Event) => {
    const target = event.target as HTMLInputElement
    const file = target.files?.[0]

    if (!file) return

    isRecognizing.value = true

    try {
        // 将图片转换为 base64
        const base64Data = await fileToBase64(file)

        // 调用 AI 识别
        const recognizedIngredients = await recognizeIngredientsFromImage(base64Data)

        // 将识别到的食材添加到列表
        if (recognizedIngredients && recognizedIngredients.length > 0) {
            recognizedIngredients.forEach(ingredient => {
                if (!ingredients.value.includes(ingredient) && ingredients.value.length < 10) {
                    ingredients.value.push(ingredient)
                }
            })

            // 显示成功提示
            alert(`成功识别到 ${recognizedIngredients.length} 种食材：${recognizedIngredients.join('、')}`)
        } else {
            alert('未能识别到食材，请尝试拍摄更清晰的照片')
        }
    } catch (error) {
        console.error('图片识别失败:', error)
        alert('图片识别失败，请重试')
    } finally {
        isRecognizing.value = false
        // 清空 input，允许重复上传同一文件
        target.value = ''
    }
}

// 将文件转换为 base64
const fileToBase64 = (file: File): Promise<string> => {
    return new Promise((resolve, reject) => {
        const reader = new FileReader()
        reader.onload = () => {
            const base64 = reader.result as string
            // 移除 data:image/xxx;base64, 前缀
            const base64Data = base64.split(',')[1]
            resolve(base64Data)
        }
        reader.onerror = reject
        reader.readAsDataURL(file)
    })
}

// 调用 AI 识别图片中的食材
const recognizeIngredientsFromImage = async (base64Data: string): Promise<string[]> => {
    const apiKey = import.meta.env.VITE_IMAGE_GENERATION_API_KEY

    const systemPrompt = `你是一个专业的冰箱食材识别系统。请严格遵循以下规则处理图片：

识别规则：
1. 仅识别明确可见、可辨认的食材
2. 名称使用常见中文名称（如：西兰花，非“青花菜”）

输出规范：
- 格式：纯文本，食材名称用逗号分隔
- 数量：最多20种，按视觉显著度排序
- 空结果：若无食材则返回空字符串
- 无任何前缀/后缀说明

优先级排序：
1. 完整可见的独立食材
2. 占据画面主要区域的食材
3. 颜色/形状辨识度高的食材

示例：
输入：冰箱内景照片
输出：西红柿,鸡蛋,青椒,酸奶,牛肉

特殊处理：
- 部分可见食材：标注为“未知蔬菜/肉类”等
- 包装食品：识别可见部分（如“牛奶盒”识别为“牛奶”）
- 调味品/饮品：仅当为主要物品时识别`

    const userPrompt = '请识别图片中的所有食材，只返回食材名称，用逗号分隔'

    try {
        const response = await fetch('https://open.bigmodel.cn/api/paas/v4/chat/completions', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                Authorization: `Bearer ${apiKey}`
            },
            body: JSON.stringify({
                model: 'GLM-4.1V-Thinking-Flash',
                messages: [
                    {
                        role: 'system',
                        content: systemPrompt
                    },
                    {
                        role: 'user',
                        content: [
                            {
                                type: 'image_url',
                                image_url: {
                                    url: `data:image/jpeg;base64,${base64Data}`
                                }
                            },
                            {
                                type: 'text',
                                text: userPrompt
                            }
                        ]
                    }
                ]
            })
        })

        if (!response.ok) {
            throw new Error(`API 请求失败: ${response.status}`)
        }

        const data = await response.json()
        const content = data.choices?.[0]?.message?.content || ''

        // 解析返回的食材列表
        const ingredientsList = content
            .split(/[,，、]/)
            .map((item: string) => item.trim())
            .filter((item: string) => item.length > 0 && item.length < 10) // 过滤掉空字符串和过长的文本
            .slice(0, 10) // 最多10个

        return ingredientsList
    } catch (error) {
        console.error('AI 识别错误:', error)
        throw error
    }
}

// 切换食材选择器显示
const toggleIngredientPicker = () => {
    showIngredientPicker.value = !showIngredientPicker.value
}

// 切换预设选择器显示
const togglePresetPicker = () => {
    showPresetPicker.value = !showPresetPicker.value
}

// 清除自定义要求
const clearCustomPrompt = () => {
    customPrompt.value = ''
    showCustomPrompt.value = false
}

// 应用预设
const applyPreset = (presetPrompt: string) => {
    showCustomPrompt.value = true
    if (customPrompt.value.trim()) {
        // 如果已有内容，询问是否替换或追加
        const newContent = customPrompt.value.trim() + '，' + presetPrompt
        if (newContent.length <= 200) {
            customPrompt.value = newContent
        }
    } else {
        customPrompt.value = presetPrompt
    }
}

// 限制自定义提示词长度
const limitCustomPrompt = () => {
    if (customPrompt.value.length > 200) {
        customPrompt.value = customPrompt.value.substring(0, 200)
    }
}

// 随机灵感数据
const randomInspirations = [
    '制作一道有故事的菜，让人回味童年',
    '融合东西方口味，创新搭配',
    '用最简单的方法，做出最温暖的味道',
    '适合雨天享用的暖心料理',
    '颜值超高的网红打卡菜品',
    '妈妈味道的现代演绎',
    '适合深夜的治愈系美食',
    '带有节日氛围的特色菜',
    '健康养生的药膳料理',
    '适合野餐的便携美食',
    '有仪式感的精致小食',
    '适合看剧时享用的小零食',
    '能够暖胃暖心的汤品',
    '适合和朋友分享的下酒菜',
    '充满创意的摆盘艺术菜',
    '适合运动后补充能量的料理'
]

// 获取随机灵感
const getRandomInspiration = () => {
    showCustomPrompt.value = true
    const randomIndex = Math.floor(Math.random() * randomInspirations.length)
    const inspiration = randomInspirations[randomIndex]

    if (customPrompt.value.trim()) {
        const newContent = customPrompt.value.trim() + '，' + inspiration
        if (newContent.length <= 200) {
            customPrompt.value = newContent
        } else {
            customPrompt.value = inspiration
        }
    } else {
        customPrompt.value = inspiration
    }
}

// 选择菜系
const selectCuisine = (cuisine: CuisineType) => {
    const index = selectedCuisines.value.indexOf(cuisine.id)
    if (index > -1) {
        selectedCuisines.value.splice(index, 1)
    } else {
        selectedCuisines.value.push(cuisine.id)
    }
}

// 生成菜谱
const generateRecipes = async () => {
    if (ingredients.value.length === 0) {
        return
    }

    // 重置状态
    isLoading.value = true
    recipes.value = [] // 清空之前的菜谱
    cuisineSlots.value = [] // 清空菜系槽位
    errorMessage.value = ''
    loadingText.value = '大师正在挑选食材...' // 重置加载文字

    // 清除之前的加载定时器
    if (loadingInterval) {
        clearInterval(loadingInterval)
        loadingInterval = null
    }

    // 滚动到结果区域
    if (resultsSection.value) {
        resultsSection.value.scrollIntoView({
            behavior: 'smooth',
            block: 'start'
        })
    }

    // 检查是否有自定义提示词
    if (customPrompt.value.trim()) {
        // 使用自定义提示词生成菜谱 - 立即创建单个槽位
        cuisineSlots.value = [
            {
                id: 'custom',
                name: '自定义大师',
                loadingText: '正在根据您的要求创作...',
                progress: 0
            }
        ]
    } else {
        // 使用菜系生成菜谱 - 立即初始化菜系槽位
        let selectedCuisineObjects = cuisines.filter(c => selectedCuisines.value.includes(c.id))

        if (selectedCuisineObjects.length === 0) {
            // 随机选择2个菜系
            const shuffled = [...cuisines].sort(() => 0.5 - Math.random())
            selectedCuisineObjects = shuffled.slice(0, 2)
        }

        // 立即初始化菜系槽位，这样用户马上就能看到模块
        cuisineSlots.value = selectedCuisineObjects.map(cuisine => ({
            id: cuisine.id,
            name: cuisine.name,
            loadingText: `${cuisine.name}正在精心创作...`,
            progress: 0
        }))
    }

    try {
        if (customPrompt.value.trim()) {
            // 开始进度动画
            const progressInterval = setInterval(() => {
                if (cuisineSlots.value[0] && !cuisineSlots.value[0].recipe) {
                    cuisineSlots.value[0].progress = Math.min(cuisineSlots.value[0].progress + Math.random() * 15, 90)
                }
            }, 500)

            const customRecipe = await generateCustomRecipe(ingredients.value, customPrompt.value.trim())

            // 完成生成，更新槽位
            if (cuisineSlots.value[0]) {
                cuisineSlots.value[0].recipe = customRecipe
                cuisineSlots.value[0].progress = 100
                cuisineSlots.value[0].loadingText = '创作完成！'
            }
            recipes.value = [customRecipe]
            isLoading.value = false
            clearInterval(progressInterval)
        } else {
            // 为每个槽位启动进度动画
            const progressIntervals: NodeJS.Timeout[] = []
            cuisineSlots.value.forEach((slot, index) => {
                const interval = setInterval(() => {
                    if (!slot.recipe) {
                        slot.progress = Math.min(slot.progress + Math.random() * 10, 85)
                        // 随机更新加载文字
                        const texts = [`${slot.name}正在挑选食材...`, `${slot.name}正在调配秘制酱料...`, `${slot.name}正在掌控火候...`, `${slot.name}正在精心摆盘...`]
                        slot.loadingText = texts[Math.floor(Math.random() * texts.length)]
                    }
                }, 800 + index * 200) // 每个槽位的更新频率略有不同
                progressIntervals.push(interval)
            })

            // 获取选中的菜系对象
            const selectedCuisineObjects =
                cuisines.filter(c => selectedCuisines.value.includes(c.id)).length > 0
                    ? cuisines.filter(c => selectedCuisines.value.includes(c.id))
                    : (() => {
                          const shuffled = [...cuisines].sort(() => 0.5 - Math.random())
                          return shuffled.slice(0, 2)
                      })()

            // 使用流式生成菜谱，每完成一个就立即显示
            await generateMultipleRecipesStream(
                ingredients.value,
                selectedCuisineObjects,
                (recipe: Recipe, index: number, total: number) => {
                    // 找到对应的菜系槽位并更新
                    const targetSlot = cuisineSlots.value.find(slot => selectedCuisineObjects[index] && slot.id === selectedCuisineObjects[index].id)

                    if (targetSlot) {
                        targetSlot.recipe = recipe
                        targetSlot.progress = 100
                        targetSlot.loadingText = '创作完成！'
                    }

                    // 每生成一个菜谱就立即添加到列表中
                    recipes.value.push(recipe)

                    // 更新全局加载文字，显示进度
                    const completedCount = recipes.value.length + cuisineSlots.value.filter(slot => slot.error).length
                    loadingText.value = `已完成 ${completedCount}/${total} 道菜谱...`

                    // 检查是否所有菜系都已处理完成（成功或失败）
                    if (completedCount === total) {
                        isLoading.value = false
                        // 清理所有进度定时器
                        progressIntervals.forEach(interval => clearInterval(interval))

                        // 延迟一下再清理槽位，让用户看到完成状态
                        setTimeout(() => {
                            // 保持槽位显示，不清理，这样用户可以看到完整的生成过程
                        }, 1000)
                    }
                },
                (error: Error, index: number, _cuisine: CuisineType, total: number) => {
                    // 处理菜谱生成失败
                    const targetSlot = cuisineSlots.value.find(slot => selectedCuisineObjects[index] && slot.id === selectedCuisineObjects[index].id)

                    if (targetSlot) {
                        targetSlot.error = true
                        targetSlot.errorMessage = error.message
                        targetSlot.progress = 0
                        targetSlot.loadingText = '生成失败'
                    }

                    // 更新全局加载文字，显示进度
                    const completedCount = recipes.value.length + cuisineSlots.value.filter(slot => slot.error).length
                    loadingText.value = `已完成 ${completedCount}/${total} 道菜谱...`

                    // 检查是否所有菜系都已处理完成（成功或失败）
                    if (completedCount === total) {
                        isLoading.value = false
                        // 清理所有进度定时器
                        progressIntervals.forEach(interval => clearInterval(interval))
                    }
                },
                customPrompt.value.trim() || undefined
            )

            // 清理进度定时器
            progressIntervals.forEach(interval => clearInterval(interval))
        }
    } catch (error) {
        console.error('生成菜谱失败:', error)
        // 显示错误信息
        errorMessage.value = error instanceof Error ? error.message : 'AI生成菜谱失败，请稍后重试'
    } finally {
        // 确保加载状态被清除
        if (isLoading.value) {
            isLoading.value = false
        }
        if (loadingInterval) {
            clearInterval(loadingInterval)
            loadingInterval = null
        }
    }
}

// 重试失败的菜系
const retryFailedCuisine = async (failedSlot: CuisineSlot) => {
    // 重置错误状态
    failedSlot.error = false
    failedSlot.errorMessage = undefined
    failedSlot.progress = 0
    failedSlot.loadingText = '大师重新思考中...'

    // 找到对应的菜系信息
    const cuisine = cuisines.find(c => c.id === failedSlot.id)
    if (!cuisine) return

    // 开始进度动画
    const progressInterval = setInterval(() => {
        if (!failedSlot.recipe && !failedSlot.error) {
            failedSlot.progress = Math.min(failedSlot.progress + Math.random() * 10, 85)
        }
    }, 500)

    try {
        // 添加随机延迟
        const delay = 1000 + Math.random() * 2000
        await new Promise(resolve => setTimeout(resolve, delay))

        // 重新生成菜谱
        const recipe = customPrompt.value.trim()
            ? await generateCustomRecipe(ingredients.value, customPrompt.value.trim())
            : await generateRecipe(ingredients.value, cuisine, customPrompt.value.trim() || undefined)

        // 成功生成，更新槽位
        failedSlot.recipe = recipe
        failedSlot.progress = 100
        failedSlot.loadingText = '重新创作完成！'

        // 添加到菜谱列表
        recipes.value.push(recipe)

        clearInterval(progressInterval)
    } catch (error) {
        console.error(`重试${cuisine.name}菜谱失败:`, error)

        // 重新设置错误状态
        failedSlot.error = true
        failedSlot.errorMessage = error instanceof Error ? error.message : `${cuisine.name}还是不会这道菜`
        failedSlot.progress = 0
        failedSlot.loadingText = '重试失败'

        clearInterval(progressInterval)
    }
}

// 模拟AI调用（后续替换为真实接口）- 暂时未使用
// const simulateAICall = async () => {
//     return new Promise(resolve => {
//         setTimeout(() => {
//             // 获取要使用的菜系
//             let cuisinesToUse = cuisines.filter(c => selectedCuisines.value.includes(c.id))
//             if (cuisinesToUse.length === 0) {
//                 // 随机选择2个菜系
//                 const shuffled = [...cuisines].sort(() => 0.5 - Math.random())
//                 cuisinesToUse = shuffled.slice(0, 2)
//             }

//             // 检查是否有自定义提示词
//             let mockRecipes: Recipe[] = []

//             if (customPrompt.value.trim()) {
//                 // 生成自定义菜谱
//                 mockRecipes = [
//                     {
//                         id: `recipe-custom-${Date.now()}`,
//                         name: `自定义：${ingredients.value.join('')}料理`,
//                         cuisine: '自定义',
//                         ingredients: ingredients.value,
//                         steps: [
//                             { step: 1, description: '准备所有食材，清洗干净', time: 5 },
//                             { step: 2, description: '根据要求进行烹饪处理', time: 10 },
//                             { step: 3, description: '调味并完成最后的制作', time: 8 },
//                             { step: 4, description: '装盘即可享用', time: 2 }
//                         ],
//                         cookingTime: 25,
//                         difficulty: 'medium',
//                         tips: ['根据个人喜好调整口味', '注意食材的新鲜度', '掌握好火候'],
//                         nutritionAnalysis: generateMockNutrition(ingredients.value)
//                     }
//                 ]
//             } else {
//                 // 生成菜系菜谱
//                 mockRecipes = cuisinesToUse.map((cuisine, index) => {
//                     return {
//                         id: `recipe-${cuisine.id}-${Date.now()}-${index}`,
//                         name: `${cuisine.name}推荐：${ingredients.value.join('')}料理`,
//                         cuisine: cuisine.name,
//                         ingredients: ingredients.value,
//                         steps: [
//                             { step: 1, description: '准备所有食材，清洗干净', time: 5 },
//                             { step: 2, description: '热锅下油，爆香配料', time: 3 },
//                             { step: 3, description: '下主料翻炒至半熟', time: 8 },
//                             { step: 4, description: '调味炒制至熟透', time: 5 },
//                             { step: 5, description: '装盘即可享用', time: 1 }
//                         ],
//                         cookingTime: 22,
//                         difficulty: 'medium',
//                         tips: ['火候要掌握好，避免炒糊', '调料要适量，突出食材本味', '炒制过程中要勤翻动'],
//                         nutritionAnalysis: generateMockNutrition(ingredients.value)
//                     }
//                 })
//             }

//             recipes.value = mockRecipes
//             resolve(mockRecipes)
//         }, 3000)
//     })
// }

// 生成模拟营养分析数据 - 暂时未使用
// const generateMockNutrition = (ingredients: string[]): NutritionAnalysis => {
//     // 基于食材数量和类型估算营养成分
//     const baseCalories = ingredients.length * 50 + Math.floor(Math.random() * 100) + 200
//     const hasVegetables = ingredients.some(ing => ['菜', '瓜', '豆', '萝卜', '白菜', '菠菜', '西红柿', '黄瓜', '茄子', '土豆'].some(veg => ing.includes(veg)))
//     const hasMeat = ingredients.some(ing => ['肉', '鸡', '鱼', '虾', '蛋', '牛', '猪', '羊'].some(meat => ing.includes(meat)))
//     const hasGrains = ingredients.some(ing => ['米', '面', '粉', '饭', '面条', '馒头'].some(grain => ing.includes(grain)))

//     // 生成饮食标签
//     const dietaryTags: string[] = []
//     if (hasVegetables && !hasMeat) dietaryTags.push('素食')
//     if (hasMeat) dietaryTags.push('高蛋白')
//     if (hasVegetables) dietaryTags.push('富含维生素')
//     if (!hasGrains) dietaryTags.push('低碳水')
//     if (baseCalories < 300) dietaryTags.push('低卡路里')

//     // 生成营养建议
//     const balanceAdvice: string[] = []
//     if (!hasVegetables) balanceAdvice.push('建议搭配新鲜蔬菜增加维生素和膳食纤维')
//     if (!hasMeat && !ingredients.some(ing => ['豆', '蛋', '奶'].some(protein => ing.includes(protein)))) {
//         balanceAdvice.push('建议增加蛋白质来源，如豆类或蛋类')
//     }
//     if (hasGrains && hasMeat) balanceAdvice.push('营养搭配均衡，适合日常食用')
//     if (ingredients.length > 5) balanceAdvice.push('食材丰富，营养全面')

//     return {
//         nutrition: {
//             calories: baseCalories,
//             protein: hasMeat ? 20 + Math.floor(Math.random() * 15) : 8 + Math.floor(Math.random() * 8),
//             carbs: hasGrains ? 35 + Math.floor(Math.random() * 20) : 15 + Math.floor(Math.random() * 10),
//             fat: hasMeat ? 12 + Math.floor(Math.random() * 8) : 5 + Math.floor(Math.random() * 5),
//             fiber: hasVegetables ? 6 + Math.floor(Math.random() * 4) : 2 + Math.floor(Math.random() * 2),
//             sodium: 600 + Math.floor(Math.random() * 400),
//             sugar: 3 + Math.floor(Math.random() * 5),
//             vitaminC: hasVegetables ? 20 + Math.floor(Math.random() * 30) : undefined,
//             calcium: hasMeat || ingredients.some(ing => ['奶', '豆'].some(ca => ing.includes(ca))) ? 100 + Math.floor(Math.random() * 100) : undefined,
//             iron: hasMeat ? 2 + Math.floor(Math.random() * 3) : undefined
//         },
//         healthScore: Math.floor(Math.random() * 3) + (hasVegetables ? 6 : 4) + (hasMeat ? 1 : 0),
//         balanceAdvice: balanceAdvice.length > 0 ? balanceAdvice : ['营养搭配合理，可以放心享用'],
//         dietaryTags: dietaryTags.length > 0 ? dietaryTags : ['家常菜'],
//         servingSize: '1人份'
//     }
// }

onUnmounted(() => {
    if (loadingInterval) {
        clearInterval(loadingInterval)
    }
})
</script>
