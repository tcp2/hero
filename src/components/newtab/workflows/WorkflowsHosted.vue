<template>
  <shared-card
    v-for="workflow in workflows"
    :key="workflow.hostId"
    :data="workflow"
    :menu="menu"
    @execute="executeWorkflow(workflow)"
    @click="$router.push(`/workflows/${$event.hostId}/host`)"
    @menuSelected="deleteWorkflow(workflow)"
  />
</template>
<script setup>
import { computed } from 'vue';
import { useI18n } from 'vue-i18n';
import { useDialog } from '@/composable/dialog';
import { arraySorter } from '@/utils/helper';
import { useHostedWorkflowStore } from '@/stores/hostedWorkflow';
import SharedCard from '@/components/newtab/shared/SharedCard.vue';
import { sendMessage } from '@/utils/message';

const props = defineProps({
  search: {
    type: String,
    default: '',
  },
  sort: {
    type: Object,
    default: () => ({
      by: '',
      order: '',
    }),
  },
});

const { t } = useI18n();
const dialog = useDialog();
const hostedWorkflowStore = useHostedWorkflowStore();

const menu = [
  { id: 'delete', name: t('common.delete'), icon: 'riDeleteBin7Line' },
];

const workflows = computed(() => {
  const filtered = hostedWorkflowStore.toArray.filter((item) => {
    if (!item?.name) return false;

    return item.name
      .toLocaleLowerCase()
      .includes(props.search.toLocaleLowerCase());
  });

  return arraySorter({
    data: filtered,
    key: props.sort.by,
    order: props.sort.order,
  });
});

async function deleteWorkflow(workflow) {
  dialog.confirm({
    title: t('workflow.delete'),
    okVariant: 'danger',
    body: t('message.delete', { name: workflow.name }),
    onConfirm: async () => {
      try {
        await hostedWorkflowStore.delete(workflow.hostId);
      } catch (error) {
        console.error(error);
      }
    },
  });
}

async function executeWorkflow(workflow) {
  await sendMessage('workflow:execute', workflow, 'background');
}
</script>
