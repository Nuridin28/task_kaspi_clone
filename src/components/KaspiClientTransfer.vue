<script setup lang="ts">
import { ref, computed } from "vue";

const activeTab = ref("phone");
const phoneNumber = ref("");
const amount = ref("");
const message = ref("");
const quickMessages = ["Рақмет!", "За обед", "Возвращаю :)"];

const formattedAmount = computed(() => {
  if (!amount.value) return "0";
  return Number(amount.value).toLocaleString("ru-RU");
});

const messageLength = computed(() => message.value.length);

const formatPhoneNumber = (value: string) => {
  const numbers = value.replace(/\D/g, "");
  if (numbers.length <= 3) return numbers;
  if (numbers.length <= 6) return `${numbers.slice(0, 3)} ${numbers.slice(3)}`;
  if (numbers.length <= 8)
    return `${numbers.slice(0, 3)} ${numbers.slice(3, 6)} ${numbers.slice(6)}`;
  return `${numbers.slice(0, 3)} ${numbers.slice(3, 6)} ${numbers.slice(
    6,
    8
  )} ${numbers.slice(8, 10)}`;
};

const handlePhoneInput = (e: Event) => {
  const input = e.target as HTMLInputElement;
  phoneNumber.value = formatPhoneNumber(input.value);
};

const selectQuickMessage = (msg: string) => {
  message.value = msg;
};

const handleTransfer = () => {
  if (!amount.value || !phoneNumber.value) return;
  console.log("Transfer:", {
    amount: amount.value,
    phoneNumber: phoneNumber.value,
    message: message.value,
  });
};
</script>

<template>
  <div class="min-h-screen bg-gray-100">
    <div class="bg-white px-4 py-3 flex items-center space-x-4">
      <button class="text-gray-800" @click="$emit('back')">←</button>
      <h1 class="text-xl font-medium">Клиенту Kaspi</h1>
    </div>

    <div class="bg-white mt-2 p-4">
      <div class="flex items-center justify-between">
        <div class="flex items-center space-x-3">
          <div
            class="bg-yellow-500 w-10 h-10 rounded-lg flex items-center justify-center text-white"
          >
            👥
          </div>
          <span class="font-medium">Kaspi Gold</span>
        </div>
        <span class="text-lg font-medium">262,70 ₸</span>
      </div>
    </div>

    <div class="bg-white mt-2 p-4">
      <div class="flex space-x-2 mb-6">
        <button
          class="flex-1 py-2 text-center rounded-lg"
          :class="activeTab === 'phone' ? 'bg-gray-100 text-blue-600' : ''"
          @click="activeTab = 'phone'"
        >
          Телефон
        </button>
        <button
          class="flex-1 py-2 text-center rounded-lg"
          :class="activeTab === 'card' ? 'bg-gray-100 text-blue-600' : ''"
          @click="activeTab = 'card'"
        >
          Карта
        </button>
        <button
          class="flex-1 py-2 text-center rounded-lg flex items-center justify-center"
          :class="activeTab === 'qr' ? 'bg-gray-100 text-blue-600' : ''"
          @click="activeTab = 'qr'"
        >
          <span class="text-xl">📱</span> Kaspi QR
        </button>
      </div>

      <div v-if="activeTab === 'phone'" class="mb-4">
        <label class="text-sm text-blue-600">Телефон получателя</label>
        <div class="flex items-center mt-1 border-b border-blue-600">
          <span class="text-gray-600">+7</span>
          <input
            type="tel"
            v-model="phoneNumber"
            @input="handlePhoneInput"
            placeholder="___ ___ __ __"
            maxlength="15"
            class="flex-1 bg-transparent outline-none pl-1 pr-2 py-1"
          />
          <button class="text-red-500 -ml-1">👤</button>
        </div>
      </div>

      <div v-if="activeTab === 'card'" class="mb-4">
        <label class="text-sm text-blue-600">Kaspi Gold получателя</label>
        <div class="flex items-center mt-1 border-b border-blue-600">
          <div
            class="bg-yellow-500 w-8 h-8 rounded-lg flex items-center justify-center text-white mr-2"
          >
            👥
          </div>
          <input
            type="text"
            placeholder="Введите номер карты"
            class="flex-1 bg-transparent outline-none px-2 py-1"
          />
          <button class="text-red-500">NFC</button>
        </div>
      </div>

      <div v-if="activeTab === 'qr'" class="mb-4">
        <div class="bg-black rounded-lg p-4 aspect-square relative">
          <div class="absolute inset-0 flex items-center justify-center">
            <div class="w-48 h-48 border-2 border-red-500 rounded-lg relative">
              <div
                class="absolute -top-1 -left-1 w-4 h-4 border-t-2 border-l-2 border-red-500"
              ></div>
              <div
                class="absolute -top-1 -right-1 w-4 h-4 border-t-2 border-r-2 border-red-500"
              ></div>
              <div
                class="absolute -bottom-1 -left-1 w-4 h-4 border-b-2 border-l-2 border-red-500"
              ></div>
              <div
                class="absolute -bottom-1 -right-1 w-4 h-4 border-b-2 border-r-2 border-red-500"
              ></div>
            </div>
          </div>
          <div class="absolute bottom-4 left-1/2 transform -translate-x-1/2">
            <button class="bg-gray-700 rounded-full p-2">
              <span class="text-xl">⚡</span>
            </button>
          </div>
        </div>
        <div class="mt-4">
          <h3 class="font-medium mb-2">Оформить онлайн</h3>
          <div class="space-y-2">
            <div class="flex items-center space-x-2 bg-gray-100 p-2 rounded-lg">
              <span class="bg-red-500 text-white text-xs px-2 py-1 rounded"
                >0-0-12</span
              >
              <span>Рассрочка 0-0-12</span>
              <span class="ml-auto">→</span>
            </div>
            <div class="flex items-center space-x-2 bg-gray-100 p-2 rounded-lg">
              <span class="bg-red-500 text-white text-xs px-2 py-1 rounded"
                >Red+</span
              >
              <span>Kaspi Red+ 0-0-4</span>
              <span class="ml-auto">→</span>
            </div>
          </div>
        </div>
      </div>

      <div
        v-if="activeTab !== 'qr'"
        class="flex bg-gray-100 rounded-lg p-4 mb-4"
      >
        <input
          type="number"
          v-model="amount"
          placeholder="0"
          class="text-2xl font-medium bg-transparent w-full outline-none no-spinner"
        />
        <span class="text-2xl font-medium">₸</span>
      </div>

      <div v-if="activeTab !== 'qr'" class="bg-gray-100 rounded-lg p-4 mb-4">
        <input
          type="text"
          v-model="message"
          placeholder="Сообщение получателю"
          maxlength="50"
          class="w-full bg-transparent outline-none"
        />
        <div class="text-right text-gray-400 text-sm">
          {{ messageLength }}/50
        </div>
      </div>

      <div v-if="activeTab !== 'qr'" class="flex space-x-2 mb-6">
        <button
          v-for="msg in quickMessages"
          :key="msg"
          @click="selectQuickMessage(msg)"
          class="px-4 py-2 bg-gray-100 rounded-full text-sm"
        >
          {{ msg }}
        </button>
      </div>
    </div>

    <div
      v-if="activeTab !== 'qr'"
      class="fixed bottom-0 left-0 right-0 p-4 bg-white border-t"
    >
      <button
        class="w-full bg-blue-600 text-white py-3 rounded-lg font-medium"
        @click="handleTransfer"
      >
        Перевести {{ formattedAmount }} ₸
      </button>
    </div>
  </div>
</template>
