<template>
  <div class="bg-white shadow rounded-lg overflow-hidden">
    <div class="px-4 py-5 sm:px-6" :class="headerBgClass">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-medium text-gray-900">{{ title }}</h2>
          <button 
            @click="showResolved = !showResolved"
            class="mt-2 inline-flex items-center px-3 py-1 rounded-full text-xs font-medium transition-colors duration-200 focus:outline-none"
            :class="[
              resolvedCount > 0 
                ? 'bg-green-100 text-green-800 hover:bg-green-200' 
                : 'bg-red-100 text-red-800 hover:bg-red-200'
            ]"
          >
            Resolved ({{ resolvedCount }})
          </button>
        </div>
        <BaseButton 
          @click="openAddModal" 
          variant="primary"
        >
          Add Prayer
        </BaseButton>
      </div>
    </div>
    <div class="px-4 py-2">
      <div v-if="prayers.length === 0" class="text-center py-4 text-gray-500">
        No prayers added yet
      </div>
      <ul v-else class="divide-y divide-gray-200">
        <li v-for="prayer in prayers" :key="prayer.id" class="py-3">
          <div class="flex items-center justify-between">
            <div class="flex-1 min-w-0 ">
              <p class="text-sm font-medium text-gray-900" :class="{ 'line-through': prayer.resolved }">
                {{ prayer.person_name }}
              </p>
              <p v-if="prayer.note" class="mt-1 text-sm text-gray-500" :class="{ 'line-through': prayer.resolved }">
                {{ prayer.note }}
              </p>
              <span v-if="prayer.resolved" class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-green-100 text-green-800 mt-2">
                {{ category === 'unbelievers' ? 'Converted' : 'Resolved' }}
              </span>
            </div>
            <div class="flex space-x-2">
              <button 
                @click="prayerActions.toggleResolved(prayer)" 
                class="p-1 rounded-full text-gray-400 hover:text-green-500 focus:outline-none"
                :title="prayer.resolved ? (category === 'unbelievers' ? 'Mark as not converted' : 'Mark as unresolved') : (category === 'unbelievers' ? 'Mark as converted' : 'Mark as resolved')"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                </svg>
              </button>
              <button 
                @click="openEditModal(prayer)" 
                class="p-1 rounded-full text-gray-400 hover:text-blue-500 focus:outline-none"
                title="Edit prayer"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                </svg>
              </button>
              <button 
                @click="prayerActions.deletePrayer(prayer)" 
                class="p-1 rounded-full text-gray-400 hover:text-red-500 focus:outline-none"
                title="Delete prayer"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                </svg>
              </button>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { computed, inject, ref } from 'vue';
import { usePrayerStore } from '../../stores/prayerStore';
import BaseButton from '../ui/BaseButton.vue';

const props = defineProps({
  title: {
    type: String,
    required: true
  },
  category: {
    type: String,
    required: true
  },
  color: {
    type: String,
    default: 'indigo'
  }
});

const prayerStore = usePrayerStore();
const modalFunctions = inject('modalFunctions');
const prayerActions = inject('prayerActions');

const showResolved = ref(false);

// Get filtered prayers from store
const prayers = computed(() => 
  prayerStore.prayersByCategory(props.category, showResolved.value)
);

// Get resolved count from store
const resolvedCount = computed(() => 
  prayerStore.resolvedCountByCategory(props.category)
);

const headerBgClass = computed(() => {
  if (props.color === 'purple') return 'bg-gradient-to-r from-purple-50 to-purple-100';
  return 'bg-gradient-to-r from-indigo-50 to-indigo-100'; // Default to indigo
});

// Functions that interact with the modal
const openAddModal = () => {
  modalFunctions.openAddModal(props.category);
};

const openEditModal = (prayer) => {
  modalFunctions.openEditModal(prayer);
};
</script> 