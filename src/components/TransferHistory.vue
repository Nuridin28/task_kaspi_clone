<script setup lang="ts">
import { ref, computed } from "vue";
import {
  Search,
  Calendar,
  Globe,
  QrCode,
  UserSearch,
  CreditCard,
  Repeat,
  ArrowLeft,
  ArrowRight,
} from "lucide-static";

interface Transfer {
  id: number;
  date: string;
  type: string;
  from: string;
  to: string;
  amount: string;
  description: string;
}

const searchQuery = ref("");
const selectedDateRange = ref("5 апреля - 12 апреля");
const showDateFilter = ref(false);
const showCalendar = ref(false);
const selectedFilter = ref("week");

// Calendar state
const currentMonth = ref(new Date());
const startDate = ref<Date | null>(null);
const endDate = ref<Date | null>(null);

const months = [
  "Январь",
  "Февраль",
  "Март",
  "Апрель",
  "Май",
  "Июнь",
  "Июль",
  "Август",
  "Сентябрь",
  "Октябрь",
  "Ноябрь",
  "Декабрь",
];

const getIconByFrom = (from: string) => {
  if (from === "Между своими счетами") return Repeat;
  if (from === "Kaspi Gold") return UserSearch;
  if (from.includes("другого банка")) return CreditCard;
  if (from.includes("Международные переводы")) return Globe;
  if (from.includes("Kaspi QR")) return QrCode;
  return Repeat; // fallback
};

// Mock data with different dates
const allTransfers: Transfer[] = [
  // April 12 transfers
  {
    id: 1,
    date: "12 апреля",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "1 500 ₸",
    description: "Между своими счетами",
  },
  {
    id: 2,
    date: "12 апреля",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "6 500 ₸",
    description: "Между своими счетами",
  },
  // April 11 transfers
  {
    id: 3,
    date: "11 апреля",
    type: "Kaspi Gold",
    from: "Kaspi Gold",
    to: "Азим Н.",
    amount: "2 000 ₸",
    description: "Клиенту Kaspi",
  },
  // April 10 transfers
  {
    id: 4,
    date: "10 апреля",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "500 ₸",
    description: "Между своими счетами",
  },
  // April 5 transfers
  {
    id: 5,
    date: "5 апреля",
    type: "Kaspi Gold",
    from: "Kaspi Gold",
    to: "Алия К.",
    amount: "15 000 ₸",
    description: "Клиенту Kaspi",
  },
  // March 30 transfers
  {
    id: 6,
    date: "30 марта",
    type: "Депозит KZT",
    from: "Между своими счетами",
    to: "Kaspi Gold",
    amount: "10 000 ₸",
    description: "Между своими счетами",
  },
];

const dateFilters = [
  { id: "week", label: "За неделю", range: "5 апреля - 12 апреля" },
  { id: "month", label: "За месяц", range: "12 марта - 12 апреля" },
  { id: "period", label: "За период", range: "Выберите период" },
];

// Calendar functions
const getDaysInMonth = (date: Date) => {
  return new Date(date.getFullYear(), date.getMonth() + 1, 0).getDate();
};

const getFirstDayOfMonth = (date: Date) => {
  return new Date(date.getFullYear(), date.getMonth(), 1).getDay();
};

const calendarDays = computed(() => {
  const days = [];
  const daysInMonth = getDaysInMonth(currentMonth.value);
  const firstDay = getFirstDayOfMonth(currentMonth.value);

  // Add empty cells for days before the first day of the month
  for (let i = 0; i < firstDay; i++) {
    days.push(null);
  }

  // Add the days of the month
  for (let i = 1; i <= daysInMonth; i++) {
    days.push(
      new Date(
        currentMonth.value.getFullYear(),
        currentMonth.value.getMonth(),
        i
      )
    );
  }

  return days;
});

const formatDate = (date: Date) => {
  const day = date.getDate();
  const month = months[date.getMonth()];
  return `${day} ${month}`;
};

const isDateSelected = (date: Date | null) => {
  if (!date) return false;

  if (startDate.value && endDate.value) {
    return date >= startDate.value && date <= endDate.value;
  }

  return startDate.value?.getTime() === date.getTime();
};

const isDateHighlighted = (date: Date | null) => {
  if (!date || !startDate.value) return false;
  return (
    date.getTime() === startDate.value.getTime() ||
    date.getTime() === endDate.value?.getTime()
  );
};

const handleDateClick = (date: Date | null) => {
  if (!date) return;

  if (!startDate.value || endDate.value) {
    startDate.value = date;
    endDate.value = null;
  } else if (date < startDate.value) {
    endDate.value = startDate.value;
    startDate.value = date;
  } else {
    endDate.value = date;
  }

  if (startDate.value && endDate.value) {
    selectedDateRange.value = `${formatDate(startDate.value)} - ${formatDate(
      endDate.value
    )}`;
  }
};

const changeMonth = (delta: number) => {
  const newDate = new Date(currentMonth.value);
  newDate.setMonth(newDate.getMonth() + delta);
  currentMonth.value = newDate;
};

// Filter transfers based on selected date range and search query
const filteredTransfers = computed(() => {
  let filtered = [...allTransfers];

  // Apply date filter
  switch (selectedFilter.value) {
    case "week":
      filtered = filtered.filter(
        (t) =>
          t.date.includes("апреля") &&
          parseInt(t.date) >= 5 &&
          parseInt(t.date) <= 12
      );
      break;
    case "month":
      filtered = filtered.filter(
        (t) =>
          t.date.includes("апреля") ||
          (t.date.includes("марта") && parseInt(t.date) >= 12)
      );
      break;
  }

  // Apply search filter if there's a search query
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase();
    filtered = filtered.filter(
      (t) =>
        t.type.toLowerCase().includes(query) ||
        t.to.toLowerCase().includes(query) ||
        t.description.toLowerCase().includes(query) ||
        t.amount.toLowerCase().includes(query)
    );
  }

  return filtered;
});

// Update date range when filter changes
const updateDateRange = () => {
  const filter = dateFilters.find((f) => f.id === selectedFilter.value);
  if (filter) {
    selectedDateRange.value = filter.range;
  }
};

const handleFilterClick = (filterId: string) => {
  selectedFilter.value = filterId;
  if (filterId === "period") {
    showCalendar.value = true;
  } else {
    updateDateRange();
  }
};

const applyFilter = () => {
  if (
    selectedFilter.value === "period" &&
    (!startDate.value || !endDate.value)
  ) {
    return;
  }
  updateDateRange();
  showDateFilter.value = false;
  showCalendar.value = false;
};

const resetFilter = () => {
  selectedFilter.value = "week";
  startDate.value = null;
  endDate.value = null;
  updateDateRange();
};
</script>

<template>
  <div class="min-h-screen bg-gray-100">
    <!-- Header -->
    <div class="bg-white px-4 py-3 flex items-center space-x-4">
      <button class="text-gray-800" @click="$emit('back')">
        <span v-html="ArrowLeft" />
      </button>
      <h1 class="text-xl font-medium">Переводы</h1>
    </div>

    <!-- Tabs -->
    <div class="bg-white mt-2 px-4 py-2 flex space-x-4">
      <button
        class="px-4 py-2 text-gray-500 rounded-full text-sm"
        @click="$emit('back')"
      >
        Мои переводы
      </button>
      <button class="px-4 py-2 text-red-500 bg-gray-100 rounded-full text-sm">
        История
      </button>
    </div>

    <!-- Search -->
    <div class="bg-white mt-2 px-4 py-3">
      <div class="flex items-center bg-gray-100 rounded-lg px-4 py-2">
        <span class="text-gray-400 mr-2"
          ><span class="text-2xl" v-html="Search" />
        </span>
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Поиск по переводам"
          class="bg-transparent w-full outline-none"
        />
      </div>
    </div>

    <!-- Date Range -->
    <div class="bg-white mt-2 px-4 py-3" @click="showDateFilter = true">
      <div class="flex items-center text-red-500">
        <span class="text-gray-400 mr-2"
          ><span class="text-2xl text-red-500" v-html="Calendar" />
        </span>
        <span class="text-black">{{ selectedDateRange }}</span>
      </div>
    </div>

    <!-- Transfers List -->
    <div class="bg-white mt-2">
      <template
        v-for="(transfer, index) in filteredTransfers"
        :key="transfer.id"
      >
        <div
          v-if="
            index === 0 || filteredTransfers[index - 1].date !== transfer.date
          "
          class="px-4 py-2 bg-gray-50 text-sm font-medium"
        >
          {{ transfer.date }}
        </div>
        <div class="px-4 py-3 flex items-center justify-between border-b">
          <div class="flex items-center space-x-3">
            <span
              class="text-2xl text-gray-400"
              v-html="getIconByFrom(transfer.from)"
            />

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

    <!-- Date Filter Modal -->
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
            @click="handleFilterClick(filter.id)"
          >
            <div
              v-if="selectedFilter === filter.id"
              class="w-3 h-3 rounded-full bg-red-500"
            ></div>
          </div>
          <span>{{ filter.label }}</span>
          <span v-if="filter.id === 'period'" class="ml-auto"
            ><span v-html="ArrowRight"
          /></span>
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

    <!-- Calendar Modal -->
    <div v-if="showCalendar" class="fixed inset-0 bg-white">
      <div class="px-4 py-3 flex items-center justify-between border-b">
        <button class="text-blue-600" @click="showCalendar = false">
          Назад
        </button>
        <h2 class="text-xl font-medium">Выберите период</h2>
        <button class="text-gray-400" @click="showCalendar = false">✕</button>
      </div>

      <div class="p-4">
        <!-- Month Navigation -->
        <div class="flex items-center justify-between mb-4">
          <button @click="changeMonth(-1)" class="text-gray-600">←</button>
          <span class="font-medium"
            >{{ months[currentMonth.getMonth()] }}
            {{ currentMonth.getFullYear() }}</span
          >
          <button @click="changeMonth(1)" class="text-gray-600">→</button>
        </div>

        <!-- Calendar Grid -->
        <div class="grid grid-cols-7 gap-1">
          <!-- Weekday Headers -->
          <div
            v-for="day in ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс']"
            :key="day"
            class="text-center text-sm text-gray-500 py-2"
          >
            {{ day }}
          </div>

          <!-- Calendar Days -->
          <div
            v-for="(date, index) in calendarDays"
            :key="index"
            class="aspect-square flex items-center justify-center relative"
            @click="handleDateClick(date)"
          >
            <div
              v-if="date"
              class="w-10 h-10 flex items-center justify-center rounded-full"
              :class="{
                'bg-red-500 text-white': isDateHighlighted(date),
                'bg-gray-100': isDateSelected(date) && !isDateHighlighted(date),
              }"
            >
              {{ date.getDate() }}
            </div>
          </div>
        </div>

        <!-- Selected Range Display -->
        <div v-if="startDate || endDate" class="mt-4 text-center text-gray-600">
          {{ startDate ? formatDate(startDate) : "" }}
          {{ startDate && endDate ? " - " : "" }}
          {{ endDate ? formatDate(endDate) : "" }}
        </div>
      </div>

      <div class="fixed bottom-0 left-0 right-0 p-4">
        <button
          class="w-full bg-blue-600 text-white py-3 rounded-lg"
          @click="applyFilter"
          :disabled="!startDate || !endDate"
        >
          Применить
        </button>
      </div>
    </div>
  </div>
</template>
