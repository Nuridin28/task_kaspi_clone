<script setup lang="ts">
import { ref } from "vue";
import KaspiClientTransfer from "./components/KaspiClientTransfer.vue";
import TransferHistory from "./components/TransferHistory.vue";
import {
  Globe,
  QrCode,
  UserSearch,
  CreditCard,
  Repeat,
  Home,
  MessageSquareMore,
  Menu,
  ArrowLeft,
  ArrowRight,
} from "lucide-static";

const currentPage = ref("main");
const activeTab = ref("transfers");

const transferOptions = [
  {
    id: 1,
    title: "Между своими счетами",
    iconComponent: Repeat,
    color: "text-red-500",
  },
  {
    id: 2,
    title: "Клиенту Kaspi",
    subtitle: "На карту Kaspi Gold",
    iconComponent: UserSearch,
    color: "text-red-500",
  },
  {
    id: 3,
    title: "Карта другого банка",
    subtitle: "С карты на карту",
    iconComponent: CreditCard,
    color: "text-red-500",
  },
  {
    id: 4,
    title: "Международные переводы",
    subtitle: "По номеру карты или телефона",
    iconComponent: Globe,
    color: "text-red-500",
  },
  {
    id: 5,
    title: "Kaspi QR",
    subtitle: "Сканируйте и платите",
    iconComponent: QrCode,
    color: "text-red-500",
  },
];

const handleTransferClick = (id: number) => {
  if (id === 2) {
    currentPage.value = "kaspiClient";
  }
};

const goToMain = () => {
  currentPage.value = "main";
  activeTab.value = "transfers";
};

const showHistory = () => {
  activeTab.value = "history";
};
</script>

<template>
  <KaspiClientTransfer v-if="currentPage === 'kaspiClient'" @back="goToMain" />
  <TransferHistory v-else-if="activeTab === 'history'" @back="goToMain" />

  <div v-else class="min-h-screen bg-gray-100">
    <div class="bg-white px-4 py-3 flex items-center space-x-4">
      <button class="text-gray-800"><span v-html="ArrowLeft" /></button>
      <h1 class="text-xl font-medium">Переводы</h1>
    </div>

    <div class="bg-white mt-2 px-4 py-2 flex space-x-4">
      <button
        class="px-4 py-2 rounded-full text-sm"
        :class="
          activeTab === 'transfers'
            ? 'text-red-500 bg-gray-100'
            : 'text-gray-500'
        "
        @click="activeTab = 'transfers'"
      >
        Мои переводы
      </button>
      <button
        class="px-4 py-2 rounded-full text-sm"
        :class="
          activeTab === 'history' ? 'text-red-500 bg-gray-100' : 'text-gray-500'
        "
        @click="showHistory"
      >
        История
      </button>
    </div>

    <div class="bg-white mt-2 px-4 py-2">
      <div
        v-for="option in transferOptions"
        :key="option.id"
        class="flex items-center justify-between py-4 border-b last:border-b-0 cursor-pointer"
        @click="handleTransferClick(option.id)"
      >
        <div class="flex items-center space-x-4">
          <span
            :class="option.color"
            class="text-2xl"
            v-html="option.iconComponent"
          />

          <div>
            <div class="font-medium">{{ option.title }}</div>
            <div v-if="option.subtitle" class="text-sm text-gray-500">
              {{ option.subtitle }}
            </div>
          </div>
        </div>
        <button class="text-gray-400"><span v-html="ArrowRight" /></button>
      </div>
    </div>

    <div
      class="fixed bottom-0 left-0 right-0 bg-white border-t flex justify-around py-2"
    >
      <button class="flex flex-col items-center text-gray-500 text-xs">
        <span class="text-xl mb-1" v-html="Home" /> Главная
      </button>
      <button class="flex flex-col items-center text-gray-500 text-xs">
        <span class="text-xl mb-1" v-html="QrCode" /> Kaspi QR
      </button>
      <button class="flex flex-col items-center text-gray-500 text-xs">
        <span class="text-xl mb-1" v-html="MessageSquareMore" />
        Сообщения
      </button>
      <button class="flex flex-col items-center text-gray-500 text-xs">
        <span class="text-xl mb-1" v-html="Menu" />
        Сервисы
      </button>
    </div>
  </div>
</template>
