<template>
  <div v-if="modelValue" class="fixed z-10 inset-0 overflow-y-auto" aria-labelledby="modal-title" role="dialog" aria-modal="true">
    <div class="flex items-start justify-center min-h-screen pt-4 px-2 pb-20 text-center sm:items-center sm:p-0">
      <!-- Background overlay -->
      <div class="fixed inset-0" aria-hidden="true" @click="closeModal">
        <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
      </div>

      <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>

      <div class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full relative w-full mx-2 sm:mx-auto max-h-[90vh] overflow-y-auto">
        <!-- Content wrapper -->
        <div class="bg-white px-3 pt-4 pb-3 sm:p-6 sm:pb-4">
          <div class="sm:flex sm:items-start">
            <div class="mt-2 text-center sm:mt-0 sm:ml-4 sm:text-left w-full">
              <h3 class="text-lg leading-6 font-medium text-gray-900" id="modal-title">
                {{ isEditMode ? 'Edit Prayer' : 'Add New Prayer' }}
              </h3>
              
              <!-- Category Tabs (Only shown in Add mode) -->
              <div v-if="!isEditMode" class="mt-3 border-b border-gray-200 sm:mt-4">
                <div class="flex">
                  <button 
                    @click="prayer.category = 'unbelievers'" 
                    class="flex-1 text-center py-2 font-medium transition" 
                    :class="prayer.category === 'unbelievers' ? 'text-purple-600 border-b-2 border-purple-600' : 'text-gray-500 border-b-2 border-transparent hover:text-purple-600 hover:border-purple-600'"
                  >
                    Unbelievers
                  </button>
                  <button 
                    @click="prayer.category = 'brethren'"
                    class="flex-1 text-center py-2 font-medium transition"
                    :class="prayer.category === 'brethren' ? 'text-purple-600 border-b-2 border-purple-600' : 'text-gray-500 border-b-2 border-transparent hover:text-purple-600 hover:border-purple-600'"
                  >
                    Brethren
                  </button>
                </div>
              </div>
              
              <div class="mt-3 sm:mt-4">
                <form @submit.prevent="savePrayer">
                  <BaseInput
                    id="person_name"
                    label="Name"
                    v-model="prayer.person_name"
                    name="person_name"
                    required
                  />
                  <BaseTextarea
                    id="note"
                    label="Note (Optional)"
                    v-model="prayer.note"
                    name="note"
                    :rows="3"
                  />
                </form>
              </div>
            </div>
          </div>
        </div>
        <div class="bg-gray-50 px-3 py-2 sm:px-6 sm:py-3 sm:flex sm:flex-row-reverse sticky bottom-0">
          <BaseButton
            variant="primary"
            @click="savePrayer"
            :loading="prayerStore.loading"
            class="w-full sm:w-auto sm:ml-3"
          >
            {{ isEditMode ? 'Save Changes' : 'Add Prayer' }}
          </BaseButton>
          <BaseButton
            variant="secondary"
            @click="closeModal"
            class="w-full mt-3 sm:w-auto sm:mt-0 sm:ml-3"
          >
            Cancel
          </BaseButton>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';
import { usePrayerStore } from '../../stores/prayerStore';
import BaseInput from '../ui/BaseInput.vue';
import BaseTextarea from '../ui/BaseTextarea.vue';
import BaseButton from '../ui/BaseButton.vue';

const props = defineProps({
  modelValue: Boolean,
  prayerData: Object,
  isEditMode: Boolean
});

const emit = defineEmits(['update:modelValue', 'save']);

const prayerStore = usePrayerStore();
const prayer = ref({ ...props.prayerData });

// Update local prayer data when props change
watch(() => props.prayerData, (newValue) => {
  prayer.value = { ...newValue };
}, { deep: true });

// Close the modal
const closeModal = () => {
  emit('update:modelValue', false);
};

// Save the current prayer
const savePrayer = async () => {
  if (!prayer.value.person_name.trim()) {
    alert('Please enter a name');
    return;
  }

  emit('save', prayer.value);
};
</script> 