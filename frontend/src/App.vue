<script setup lang="ts">
import { computed, Ref, ref } from "@vue/reactivity";
import axios from "axios";

interface Data {
  message: string;
}

let result: Ref<string> = ref("Hi")

const getData = async () => {
  const response = await axios.get("http://0.0.0.0:8000/test")
  const data: Data = response.data
  result.value = data.message
  console.log(result.value)
}
const message = computed(() => result.value)
</script>

<template>
  <button @click="getData">
    GET
  </button>
  <div ref="message">
    The message is: {{ message }}
  </div>
</template>
