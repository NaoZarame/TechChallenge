<template>
  <div id="app">
    <h1>AI チャットボット</h1>
    <input v-model="userInput" placeholder="質問を入力してください" @keyup.enter="sendQuery" />
    <button @click="sendQuery">送信</button>

    <div v-if="loading">AIが応答しています...</div>

    <div v-if="response">
      <h3>AIの応答：</h3>
      <p>{{ response }}</p>
    </div>

    <div v-if="error" class="error">
      <p>エラーが発生しました。もう一度試してください。</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { GoogleGenerativeAI } from '@google/generative-ai';

// 変数の定義
const userInput = ref(''); // ユーザー入力
const response = ref('');  // AIの応答
const loading = ref(false); // ローディング状態
const error = ref(false); // エラーフラグ

// 質問を送信する関数
const sendQuery = async () => {
  if (!userInput.value.trim()) return; // 入力が空の場合、何もしない

  loading.value = true;
  response.value = '';
  error.value = false;

  try {
    const genAI = new GoogleGenerativeAI('AIzaSyDSEMSQ9jiQ_04mQvVU_rjXdDV7qMr-w4A');
    const model = genAI.getGenerativeModel({ model: 'gemini-1.5-flash' });

    const result = await model.generateContent(userInput.value);
    response.value = result.response.text();
  } catch (err) {
    error.value = true;
    console.error('Error occurred:', err);
  } finally {
    loading.value = false;
  }

  userInput.value = ''; // 入力をクリア
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  padding: 50px;
}

input {
  padding: 10px;
  font-size: 16px;
  margin-right: 10px;
}

button {
  padding: 10px;
  font-size: 16px;
}

div {
  margin-top: 20px;
}

.error {
  color: red;
}
</style>

