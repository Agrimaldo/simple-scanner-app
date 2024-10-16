<template>
  <div>
    <p>{{ title }}</p>
    <ul>
      <li v-for="todo in todos" :key="todo.id" @click="increment">
        {{ todo.id }} - {{ todo.content }}
      </li>
    </ul>
    <p>Count: {{ todoCount }} / {{ meta.totalCount }}</p>
    <p>Active: {{ active ? 'yes' : 'no' }}</p>
    <p>Clicks on todos: {{ clickCount }}</p>
    <div>
      <q-btn color="primary" label="Scan Barcode" @click="startScan" />
      <p v-if="scanResult">Result: {{ scanResult }} </p>
      <p v-if="scanResultError">Error: {{ scanResultError }} </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import { Todo, Meta } from './models';
import { BarcodeScanner } from '@capacitor-community/barcode-scanner';

interface Props {
  title: string;
  todos?: Todo[];
  meta: Meta;
  active: boolean;
};

const props = withDefaults(defineProps<Props>(), {
  todos: () => []
});

const clickCount = ref(0);
function increment() {
  clickCount.value += 1;
  return clickCount.value;
}

const todoCount = computed(() => props.todos.length);


//------------------------------------------------------------------------------------------------------------------------------

const scanResult = ref('');
const scanResultError = ref('');

const startScan = async () => {
  try {
    // Check camera permission
    // This is just a simple example, check out the better checks below
    await BarcodeScanner.checkPermission({ force: true });

    // make background of WebView transparent
    // note: if you are using ionic this might not be enough, check below
    BarcodeScanner.hideBackground();

    const result = await BarcodeScanner.startScan(); // start scanning and wait for a result

    // if the result has content
    if (result.hasContent) {
      scanResult.value = result.content;
      console.log(result.content); // log the raw scanned content
    }
  } catch (error:any) {
    console.error('Error scanning barcode:', error);
    scanResultError.value = error;
  }
};

</script>
