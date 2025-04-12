<script setup lang="ts">
import { ref } from "vue";

interface Transfer {
  id: number;
  date: string;
  type: string;
  from: string;
  to: string;
  amount: string;
  description: string;
  icon: string;
}

const searchQuery = ref("");
const selectedDateRange = ref("5 апреля - 12 апреля");
const showDateFilter = ref(false);

const transfers: Transfer[] = [
  {
    id: 1,
    date: "12 апреля",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "1 500,7 ₸",
    description: "Между своими счетами",
    icon: "🔄",
  },
  {
    id: 2,
    date: "12 апреля",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "6 500 ₸",
    description: "Между своими счетами",
    icon: "🔄",
  },
  {
    id: 3,
    date: "12 апреля",
    type: "Kaspi Gold",
    from: "Kaspi Gold",
    to: "Беназир Н.",
    amount: "206 ₸",
    description: "Клиенту Kaspi",
    icon: "👤",
  },
  {
    id: 4,
    date: "12 апреля",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "500 ₸",
    description: "Между своими счетами",
    icon: "🔄",
  },
];

const dateFilters = [
  { id: "week", label: "За неделю" },
  { id: "month", label: "За месяц" },
  { id: "period", label: "За период" },
];

const selectedFilter = ref("week");

const applyFilter = () => {
  showDateFilter.value = false;
};

const resetFilter = () => {
  selectedFilter.value = "week";
};
</script>

<template>
  <div class="min-h-screen bg-gray-100">
    <div class="bg-white px-4 py-3 flex items-center space-x-4">
      <button class="text-gray-800" @click="$emit('back')">←</button>
      <h1 class="text-xl font-medium">Переводы</h1>
    </div>

    <div class="bg-white mt-2 px-4 py-2 flex space-x-4">
      <button class="px-4 py-2 text-gray-500 rounded-full text-sm">
        Мои переводы
      </button>
      <button class="px-4 py-2 text-red-500 bg-gray-100 rounded-full text-sm">
        История
      </button>
    </div>

    <div class="bg-white mt-2 px-4 py-3">
      <div class="flex items-center bg-gray-100 rounded-lg px-4 py-2">
        <span class="text-gray-400 mr-2">🔍</span>
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Поиск по переводам"
          class="bg-transparent w-full outline-none"
        />
      </div>
    </div>

    <div class="bg-white mt-2 px-4 py-3" @click="showDateFilter = true">
      <div class="flex items-center text-red-500">
        <span class="mr-2">📅</span>
        <span>{{ selectedDateRange }}</span>
      </div>
    </div>

    <div class="bg-white mt-2">
      <template v-for="(transfer, index) in transfers" :key="transfer.id">
        <div
          v-if="index === 0 || transfers[index - 1].date !== transfer.date"
          class="px-4 py-2 bg-gray-50 text-sm font-medium"
        >
          {{ transfer.date }}
        </div>
        <div class="px-4 py-3 flex items-center justify-between border-b">
          <div class="flex items-center space-x-3">
            <span class="text-2xl">{{ transfer.icon }}</span>
            <div>
              <div class="font-medium">{{ transfer.type }}</div>
              <div class="text-sm text-gray-500">→ {{ transfer.to }}</div>
              <div class="text-xs text-gray-400">
                {{ transfer.description }}
              </div>
            </div>
          </div>
          <span class="font-medium">{{ transfer.amount }}</span>
        </div>
      </template>
    </div>

    <div v-if="showDateFilter" class="fixed inset-0 bg-white">
      <div class="px-4 py-3 flex items-center justify-between border-b">
        <button class="text-blue-600" @click="resetFilter">Сбросить</button>
        <h2 class="text-xl font-medium">Фильтр</h2>
        <button class="text-gray-400" @click="showDateFilter = false">✕</button>
      </div>

      <div class="p-4">
        <div
          v-for="filter in dateFilters"
          :key="filter.id"
          class="flex items-center mb-4"
        >
          <div
            class="w-5 h-5 rounded-full border-2 border-gray-300 mr-3 flex items-center justify-center"
            :class="{ 'border-red-500': selectedFilter === filter.id }"
            @click="selectedFilter = filter.id"
          >
            <div
              v-if="selectedFilter === filter.id"
              class="w-3 h-3 rounded-full bg-red-500"
            ></div>
          </div>
          <span>{{ filter.label }}</span>
          <span v-if="filter.id === 'period'" class="ml-auto">→</span>
        </div>
      </div>

      <div class="fixed bottom-0 left-0 right-0 p-4">
        <button
          class="w-full bg-blue-600 text-white py-3 rounded-lg"
          @click="applyFilter"
        >
          Применить
        </button>
      </div>
    </div>
  </div>
</template>
