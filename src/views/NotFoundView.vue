<template>
  <div
    id="notFoundView"
    style="
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 60vh;
    "
  >
    <img
      src="@/assets/404.jpg"
      alt="404 NOT FOUND"
      style="height: 200px; margin-right: 48px"
    />
    <div>
      <h1 style="font-size: 2.5rem; font-weight: bold; margin-bottom: 8px">
        找不到页面
      </h1>
      <div style="font-size: 1.3rem; color: #666; margin-bottom: 24px">
        抱歉，我们找不到您想要的页面……
      </div>
      <a-button type="outline" @click="goHome" size="large">
        <icon-home style="margin-right: 6px" />
        返回主页
      </a-button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from "vue-router";
import { IconHome } from "@arco-design/web-vue/es/icon";

const router = useRouter();
const goHome = () => {
  router.push({ name: "浏览题目" });
};
</script>
