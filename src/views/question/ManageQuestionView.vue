<template>
  <!-- eslint-disable -->
  <div id="manageQuestionView">
    <a-table
      :ref="tableRef"
      :columns="columns"
      :data="dataList"
      :pagination="{
        showTotal: true,
        pageSize: searchParams.pageSize,
        current: searchParams.current,
        total,
      }"
      @page-change="onPageChange"
    >
      <template #optional="{ record }">
        <a-space>
          <a-button type="primary" @click="doUpdate(record)"> 修改</a-button>
          <a-button status="danger" @click="doDelete(record)">删除</a-button>
        </a-space>
      </template>
    </a-table>
  </div>
</template>

<script setup lang="ts">
/* eslint-disable no-undef */
import { onMounted, ref, watchEffect, h } from "vue";
import { Question, QuestionControllerService } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";

const tableRef = ref();

const dataList = ref([]);
const total = ref(0);
const searchParams = ref({
  pageSize: 10,
  current: 1,
});

const loadData = async () => {
  const res = await QuestionControllerService.listQuestionByPageUsingPost(
    searchParams.value
  );
  if (res.code === 0) {
    dataList.value = res.data.records;
    total.value = res.data.total;
  } else {
    message.error("加载失败，" + res.message);
  }
};
watchEffect(() => {
  loadData();
});

/**
 * 页面加载时，请求数据
 */
onMounted(() => {
  loadData();
});

// {id: "1", title: "A+ D", content: "新的题目内容", tags: "["二叉树"]", answer: "新的答案", submitNum: 0,…}

const formatDateTime = (input: any) => {
  if (!input) return "";
  const d = new Date(input);
  if (Number.isNaN(d.getTime())) return String(input);
  const yyyy = d.getFullYear();
  const mm = String(d.getMonth() + 1).padStart(2, "0");
  const dd = String(d.getDate()).padStart(2, "0");
  const hh = String(d.getHours()).padStart(2, "0");
  const mi = String(d.getMinutes()).padStart(2, "0");
  return `${yyyy}-${mm}-${dd} ${hh}:${mi}`;
};

const formatUuidTwoLines = (value: unknown): string => {
  const s = String(value ?? "");
  if (!s) return "";
  const mid = Math.ceil(s.length / 2);
  return `${s.slice(0, mid)}\n${s.slice(mid)}`;
};

const columns = [
  {
    title: "id",
    dataIndex: "id",
    width: 220,
    render: ({ record }: { record: Question }) =>
      h(
        "span",
        { class: "uuid-cell" },
        formatUuidTwoLines((record as any)?.id)
      ),
  },
  {
    title: "标题",
    dataIndex: "title",
    width: 320,
  },
  {
    title: "内容",
    dataIndex: "content",
    width: 700,
  },
  {
    title: "标签",
    dataIndex: "tags",
  },
  {
    title: "答案",
    dataIndex: "answer",
  },
  {
    title: "提交数",
    dataIndex: "submitNum",
    width: 80,
    render: ({ record }: { record: Question }) =>
      h(
        "span",
        { class: "nowrap-cell" },
        String((record as any)?.submitNum ?? "")
      ),
  },
  {
    title: "通过数",
    dataIndex: "acceptedNum",
    width: 80,
    render: ({ record }: { record: Question }) =>
      h(
        "span",
        { class: "nowrap-cell" },
        String((record as any)?.acceptedNum ?? "")
      ),
  },
  {
    title: "判题配置",
    dataIndex: "judgeConfig",
    render: ({ record }: { record: Question }) => {
      const cfgRaw = (record as any)?.judgeConfig;
      const cfg =
        typeof cfgRaw === "string"
          ? (() => {
              try {
                return JSON.parse(cfgRaw);
              } catch {
                return {} as any;
              }
            })()
          : cfgRaw || ({} as any);
      const lines = [
        `"timeLimit": ${cfg.timeLimit ?? ""}`,
        `"memoryLimit": ${cfg.memoryLimit ?? ""}`,
        `"stackLimit": ${cfg.stackLimit ?? ""}`,
      ].join("\n");
      return h("pre", { class: "judge-config" }, lines);
    },
  },
  {
    title: "判题用例",
    dataIndex: "judgeCase",
  },
  {
    title: "用户id",
    dataIndex: "userId",
    width: 220,
    render: ({ record }: { record: Question }) =>
      h(
        "span",
        { class: "uuid-cell" },
        formatUuidTwoLines((record as any)?.userId)
      ),
  },
  {
    title: "创建时间",
    dataIndex: "createTime",
    width: 180,
    render: ({ record }: { record: Question }) =>
      h(
        "span",
        { class: "nowrap-cell" },
        formatDateTime((record as any)?.createTime)
      ),
  },
  {
    title: "操作",
    slotName: "optional",
  },
];

const onPageChange = (page: number) => {
  searchParams.value = {
    ...searchParams.value,
    current: page,
  };
};
const doDelete = async (question: Question) => {
  const res = await QuestionControllerService.deleteQuestionUsingPost({
    id: question.id,
  });
  if (res.code === 0) {
    message.success("删除成功");
    loadData();
  } else {
    message.error("删除失败");
  }
};

const router = useRouter();

const doUpdate = (question: Question) => {
  router.push({
    path: "/update/question",
    query: {
      id: question.id,
    },
  });
};
</script>

<style scoped>
#manageQuestionView {
}
/* 表头不换行 */
:deep(.arco-table thead .arco-table-th) {
  white-space: nowrap;
}
/* 判题配置三行展示 */
.judge-config {
  white-space: pre;
  margin: 0;
}
.nowrap-cell {
  white-space: nowrap;
  display: inline-block;
}
.uuid-cell {
  white-space: pre-line;
  word-break: break-all;
  display: inline-block;
}
</style>
