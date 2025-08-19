<template>
  <div id="questionSubmitDetail" style="max-width: 900px; margin: 0 auto">
    <h2 style="margin-bottom: 16px">
      {{ data?.questionVO?.id }}.{{ data?.questionVO?.title }}
    </h2>
    <a-card style="margin-bottom: 24px">
      <div
        style="display: flex; flex-wrap: wrap; justify-content: space-between"
      >
        <div style="min-width: 320px">
          <div style="margin-bottom: 8px">
            作者：{{ data?.userVO?.userName }}
          </div>
          <div style="margin-bottom: 8px">
            判题状态：
            <a-tag
              v-if="data?.status === 0"
              :style="{ background: '#595959', color: '#fff', border: 'none' }"
              >等待中
            </a-tag>
            <a-tag
              v-else-if="data?.status === 1"
              :style="{ background: '#d9d9d9', color: '#333', border: 'none' }"
            >
              判题中
            </a-tag>
            <a-tag
              v-else-if="data?.status === 2"
              :style="{ background: '#52c41a', color: '#fff', border: 'none' }"
            >
              成功
            </a-tag>
            <a-tag
              v-else-if="data?.status === 3"
              :style="{ background: '#ff4d4f', color: '#fff', border: 'none' }"
            >
              失败
            </a-tag>
          </div>
          <div style="margin-bottom: 8px">
            结果：{{ data?.judgeInfo?.message }}
          </div>
        </div>
        <div style="min-width: 320px">
          <div style="margin-bottom: 8px">
            运行时间：{{ data?.judgeInfo?.time }}ms
          </div>
          <div style="margin-bottom: 8px">
            内存消耗：{{ Math.floor((data?.judgeInfo?.memory || 0) / 1024) }}KB
          </div>
          <div style="margin-bottom: 8px">提交日期：{{ data?.createTime }}</div>
        </div>
      </div>
    </a-card>
    <!-- 编译错误信息 -->
    <div
      v-if="
        data?.judgeInfo?.message === 'Compile Error' ||
        data?.judgeInfo?.compileMsg
      "
      style="margin-bottom: 24px"
    >
      <div
        style="
          color: #ff4d4f;
          font-weight: bold;
          font-size: 1.2rem;
          margin-bottom: 12px;
        "
      >
        编译出错
      </div>
      <div
        style="
          background: #fff2f0;
          border: 1px solid #ffccc7;
          border-radius: 6px;
          padding: 16px;
          color: #ff4d4f;
          font-family: monospace;
          white-space: pre-wrap;
        "
      >
        {{ data?.judgeInfo?.compileMsg || "" }}
      </div>
    </div>
    <div style="font-weight: bold; margin-bottom: 8px">提交的代码：</div>
    <div style="margin-bottom: 8px; color: #888">
      语言：{{ data?.language }}
    </div>
    <pre
      style="
        background: #fafafa;
        border-radius: 6px;
        padding: 16px;
        overflow-x: auto;
        color: #222;
        font-size: 15px;
      "
      >{{ formattedCode }}
    </pre>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";
import { QuestionControllerService } from "../../../generated";
import message from "@arco-design/web-vue/es/message";

const route = useRoute();
const data = ref<any>(null);
const formattedCode = ref("");

const loadDetail = async () => {
  const submitId = route.params.submitId;
  const res = await QuestionControllerService.getQuestionSubmitVoByIdUsingGet(
    submitId as any
  );
  if (res.code === 0) {
    data.value = res.data;
    formattedCode.value = (res.data.code || "").replace(/\n/g, "\n");
  } else {
    // 跳转到404页面
    window.location.href = "/404";
  }
};

onMounted(() => {
  loadDetail();
});
</script>

<style scoped>
pre {
  white-space: pre-wrap;
  word-break: break-all;
}
</style>
