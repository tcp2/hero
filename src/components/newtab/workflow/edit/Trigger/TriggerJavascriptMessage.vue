<template>
  <div>
    <div class="mb-3">
      <label class="block text-sm font-medium text-gray-700 mb-1">{{
        t('workflow.blocks.trigger.forms.messageType')
      }}</label>
      <ui-input
        :model-value="data.messageType"
        placeholder="automa:trigger"
        class="w-full"
        @change="$emit('update', { messageType: $event })"
      />
    </div>

    <div>
      <label class="block text-sm font-medium text-gray-700 mb-1">{{
        t('workflow.blocks.trigger.forms.triggerName')
      }}</label>
      <ui-input
        :model-value="data.triggerName"
        :placeholder="t('workflow.blocks.trigger.forms.triggerNamePlaceholder')"
        class="w-full"
        @change="$emit('update', { triggerName: $event })"
      />
    </div>

    <p class="mt-3 text-sm text-gray-500">
      {{ t('workflow.blocks.trigger.javascriptMessageDescription') }}
    </p>

    <div class="mt-4">
      <div class="mb-2">
        <div class="flex items-center">
          <div class="flex-1">
            <span class="text-sm font-medium">{{
              t('workflow.blocks.trigger.javascriptMessageExamples')
            }}</span>
          </div>
          <ui-segmented-control
            v-model="activeExample"
            :options="exampleOptions"
            size="sm"
          />
        </div>
      </div>

      <!-- Window Event Listener Example -->
      <div
        v-if="activeExample === 'window'"
        class="p-3 bg-gray-50 rounded-md border border-gray-200"
      >
        <pre class="text-xs text-gray-700 overflow-auto">
// Trong trang web của bạn
window.dispatchEvent(new CustomEvent('{{
            data.messageType || 'automa:trigger'
          }}', {
  detail: {
    trigger: '{{ data.triggerName || 'your_trigger_name' }}',
    payload: { /* your data */ }
  }
}));</pre
        >
      </div>
    </div>
  </div>
</template>
<script setup>
import { useI18n } from 'vue-i18n';
import { ref } from 'vue';

defineProps({
  data: {
    type: Object,
    default: () => ({}),
  },
});
defineEmits(['update']);

const { t } = useI18n();
const activeExample = ref('chrome');
const exampleOptions = [
  { value: 'chrome', label: 'Chrome API' },
  { value: 'window', label: 'Window Event' },
  { value: 'direct', label: 'Direct Message' },
];
</script>
