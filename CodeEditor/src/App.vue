<script>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import * as monaco from 'monaco-editor'; // импорт Monaco editor

export default {
  setup() {
    const selectedLanguage = ref('python'); // по умолчанию выбранный язык 'Python'
    const code = ref('');
    const output = ref('');
    const editorContainer = ref(null);
    let editor = null;

    onMounted(async () => {
      // загрузка Monaco Editor (асинхронно)
      await import('monaco-editor/esm/vs/editor/editor.all.js');
      // создание редактора
      editor = monaco.editor.create(editorContainer.value, {
        value: code.value,
        language: selectedLanguage.value,
        theme: 'vs-dark',
        automaticLayout: true,
      });
      //обработчик изменений содержимого редактора
      editor.onDidChangeModelContent(() => {
        code.value = editor.getValue();
      });
      // при изменении содержимого и обновление модели редактора
      watchEffect(() => {
        editor.setModel(monaco.editor.createModel(code.value, selectedLanguage.value));
      });
    });

    onUnmounted(() => {
      // освобождение ресурсов при удалении компонента
      if (editor) {
        editor.dispose();
      }
    });

    const runCode = async () => {
      output.value = 'Выполнение...';
      try {
        await new Promise(resolve => setTimeout(resolve, 500)); // имитация задержки
        // проверка на пустой код
        if (code.value.trim() === '') {
          throw new Error('Код не может быть пустым!');
        }

        // Имитация успешного выполнения
        const mockResponse = { success: true, output: 'Код выполнен успешно!' };
        // обработка результата
        if (mockResponse.success) {
          output.value = mockResponse.output; // вывод результата
        } else {
          output.value = `Ошибка: ${mockResponse.error}`; // вывод ошибки
        }

      } catch (error) {
        output.value = output.value = `Ошибка: ${error.message}`;
      }
    };

    return {selectedLanguage, code, output, runCode, editorContainer}; // Возвращаем данные и методы для использования в шаблоне
  },
};
</script>


<template>
  <div class="container mx-auto p-8">
    <h1 class="text-3xl font-bold mb-4">Онлайн Редактор Кода</h1>

    <div class="mb-4">
      <label for="language-select" class="block text-gray-700 font-bold mb-2">Язык:</label>
      <select
        id="language-select"
        v-model="selectedLanguage"
        class="w-full border border-gray-300 rounded px-3 py-1"
      >
        <option value="python">Python</option>
        <option value="go">Go</option>
      </select>
    </div>

    <div class="flex">
      <div ref="editorContainer" class="w-1/2 p-4 border border-gray-300 rounded"></div>
      <div class="w-1/2 ml-4">
        <button @click="runCode" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
          Run
        </button>
        <pre class="bg-gray-100 p-4 rounded mt-4 overflow-auto h-64">
          <code class="language-text">{{ output }}</code>
        </pre>
      </div>
    </div>
  </div>
</template>
