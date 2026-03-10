<template>
  <div class="curl-cleaner-container">
    <h2>cURL 命令清理工具（适配 Postman 解析）</h2>
    <!-- 输入框：粘贴 CMD 复制的原始 cURL 命令 -->
    <textarea
      v-model="rawCurl"
      placeholder="请粘贴从 CMD 复制的原始 cURL 命令（包含 ^ 换行符的版本）..."
      class="curl-input"
    ></textarea>
    <button @click="cleanCurl" class="clean-btn">清理并生成可解析的 cURL 命令</button>
    <p class="tip">清理规则：自动删除 ^ 换行符、合并为单行、保留必要的转义符 \</p>
    <!-- 结果展示区：输出清理后的命令 -->
    <div class="result-wrapper">
      <h4>清理后的结果（可直接复制到 Postman Import → Raw text）：</h4>
      <div class="result-box" v-text="cleanedCurl"></div>
    </div>
    <button @click="copy" class="clean-btn">复制</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 响应式数据
const rawCurl = ref(''); // 原始输入的curl命令
const cleanedCurl = ref(''); // 清理后的curl命令

// 清理 cURL 命令的核心函数
const cleanCurl = () => {
  if (!rawCurl.value) {
    cleanedCurl.value = '请先粘贴原始 cURL 命令！';
    return;
  }

  let cleaned = rawCurl.value
    .replace(/\^/g, '') // 删除所有 Windows 换行符 ^
    .replace(/\r\n/g, ' ') // 将换行符替换为空格
    .replace(/\n/g, ' ') // 兼容不同换行格式
    .replace(/\s+/g, ' ') // 将多个连续空格合并为单个空格
    .trim(); // 去除首尾空格

  cleanedCurl.value = cleaned;
};
const copy = async() => {
  await navigator.clipboard.writeText(cleanedCurl.value);
  alert('复制成功！');
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Microsoft YaHei", sans-serif;
}

.curl-cleaner-container {
  width: 1000px;
  margin: 50px auto;
  padding: 0 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.curl-input {
  width: 100%;
  height: 200px;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 14px;
  line-height: 1.5;
  resize: vertical;
}

.clean-btn {
  padding: 12px 24px;
  background-color: #0078d7;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.clean-btn:hover {
  background-color: #005a9e;
}

.result-wrapper {
  width: 100%;
}

.result-box {
  width: 100%;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  min-height: 100px;
  background-color: #f9f9f9;
  word-break: break-all;
  font-family: Consolas, monospace;
}

.tip {
  color: #666;
  font-size: 12px;
  margin-top: -10px;
}
</style>