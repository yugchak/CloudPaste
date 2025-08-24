<template>
  <div class="file-upload-container mx-auto px-3 sm:px-6 flex-1 flex flex-col pt-6 sm:pt-8 w-full max-w-full sm:max-w-6xl">
    <div class="main-content" v-if="hasPermission">
      <div class="card mb-6 p-4 sm:p-6">
        <h2>test</h2>
      </div>
    </div>

    <div class="main-content" v-else>
      <div class="card mb-6 p-4 sm:p-6">
        <h2>test2</h2>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, onBeforeUnmount } from "vue";
import { api } from "@/api";
import PermissionManager from "../components/common/PermissionManager.vue";
import { useI18n } from "vue-i18n"; // 导入i18n
import { useAuthStore } from "@/stores/authStore.js";

const { t } = useI18n(); // 初始化i18n

// 使用认证Store
const authStore = useAuthStore();

const props = defineProps({
  darkMode: {
    type: Boolean,
    default: false,
  },
});

// 数据状态
const s3Configs = ref([]);
const loadingConfigs = ref(false);
const message = ref(null);

// 从Store获取权限状态的计算属性
const hasPermission = computed(() => authStore.hasFilePermission);

// API密钥验证逻辑已移至认证Store

// 导航到管理页面
const navigateToAdmin = () => {
  // 使用 Vue Router 进行导航
  import("../router").then(({ routerUtils }) => {
    routerUtils.navigateTo("admin");
  });
};

// 加载S3配置
const loadS3Configs = async () => {
  if (!hasPermission.value) return;

  loadingConfigs.value = true;
  try {
    const response = await api.file.getS3Configs();
    if (response.success && response.data) {
      s3Configs.value = response.data;
    }
  } catch (error) {
    console.error("加载S3配置失败:", error);
  } finally {
    loadingConfigs.value = false;
  }
};

</script>

<style scoped>
/* 响应式设计 */
@media (max-width: 640px) {
  .file-upload-container {
    padding-top: 1rem;
  }
}
</style>
