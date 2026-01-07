<script setup lang='ts'>
import type { IPersonConfig, IRiggedWinner } from '@/types/storeType'
import { X } from 'lucide-vue-next'
import { storeToRefs } from 'pinia'
import { computed, ref, toRefs, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import useStore from '@/store'

const props = defineProps({
    maxCount: {
        type: Number,
        default: 1,
    },
    selectedWinners: {
        type: Array<IRiggedWinner>,
        default: () => [],
    },
})
const emits = defineEmits(['update:selectedWinners', 'submitData'])
const { t } = useI18n()
const dialogRef = ref<HTMLDialogElement>()
const { maxCount, selectedWinners } = toRefs(props)
const searchKeyword = ref('')

const { personConfig } = useStore()
const { getAllPersonList: allPersonList } = storeToRefs(personConfig)

// 内部选中状态
const internalSelected = ref<IRiggedWinner[]>([])

// 过滤后的人员列表
const filteredPersonList = computed(() => {
    if (!searchKeyword.value) {
        return allPersonList.value
    }
    const keyword = searchKeyword.value.toLowerCase()
    return allPersonList.value.filter((person: IPersonConfig) =>
        person.name.toLowerCase().includes(keyword)
        || person.uid.toLowerCase().includes(keyword),
    )
})

// 是否已选中
function isSelected(person: IPersonConfig): boolean {
    return internalSelected.value.some(w => w.id === person.id)
}

// 切换选中状态
function toggleSelect(person: IPersonConfig) {
    const index = internalSelected.value.findIndex(w => w.id === person.id)
    if (index > -1) {
        internalSelected.value.splice(index, 1)
    }
    else {
        if (internalSelected.value.length >= maxCount.value) {
            return
        }
        internalSelected.value.push({
            id: person.id,
            uid: person.uid,
            name: person.name,
        })
    }
}

// 移除已选中的人员
function removeSelected(winner: IRiggedWinner) {
    const index = internalSelected.value.findIndex(w => w.id === winner.id)
    if (index > -1) {
        internalSelected.value.splice(index, 1)
    }
}

// 打开弹窗
function openDialog() {
    internalSelected.value = [...selectedWinners.value]
    searchKeyword.value = ''
    dialogRef.value?.showModal()
}

// 确认选择
function confirmSelection() {
    emits('update:selectedWinners', [...internalSelected.value])
    emits('submitData', [...internalSelected.value])
    dialogRef.value?.close()
}

// 取消选择
function cancelSelection() {
    dialogRef.value?.close()
}

// 监听外部数据变化
watch(selectedWinners, (val) => {
    internalSelected.value = [...val]
}, { immediate: true })

defineExpose({
    openDialog,
})
</script>

<template>
  <dialog ref="dialogRef" class="z-50 overflow-hidden border-none modal">
    <div class="w-full max-w-3xl overflow-hidden modal-box">
      <h3 class="pb-4 text-lg font-bold">
        {{ t('dialog.selectRiggedWinner') }}
      </h3>
      <p class="pb-2 text-sm text-gray-500">
        {{ t('dialog.riggedWinnerTip', { max: maxCount }) }}
      </p>

      <!-- 已选中的人员标签 -->
      <div v-if="internalSelected.length > 0" class="flex flex-wrap gap-2 pb-4">
        <div
          v-for="winner in internalSelected"
          :key="winner.id"
          class="flex items-center gap-1 px-2 py-1 text-sm rounded-full bg-primary/20 text-primary"
        >
          <span>{{ winner.name }} ({{ winner.uid }})</span>
          <X class="w-4 h-4 cursor-pointer hover:text-error" @click="removeSelected(winner)" />
        </div>
      </div>

      <!-- 搜索框 -->
      <div class="pb-4">
        <input
          v-model="searchKeyword"
          type="text"
          :placeholder="t('placeHolder.searchPerson')"
          class="w-full input input-bordered input-sm"
        >
      </div>

      <!-- 人员列表 -->
      <div class="overflow-y-auto max-h-64">
        <table class="table w-full table-zebra table-sm">
          <thead class="sticky top-0 bg-base-200">
            <tr>
              <th class="w-12" />
              <th>{{ t('table.uid') }}</th>
              <th>{{ t('table.name') }}</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="person in filteredPersonList"
              :key="person.id"
              class="cursor-pointer hover"
              :class="{ 'bg-primary/10': isSelected(person) }"
              @click="toggleSelect(person)"
            >
              <td>
                <input
                  type="checkbox"
                  class="checkbox checkbox-sm checkbox-primary"
                  :checked="isSelected(person)"
                  :disabled="!isSelected(person) && internalSelected.length >= maxCount"
                  @click.stop="toggleSelect(person)"
                >
              </td>
              <td>{{ person.uid }}</td>
              <td>{{ person.name }}</td>
            </tr>
          </tbody>
        </table>
        <div v-if="filteredPersonList.length === 0" class="py-8 text-center text-gray-500">
          {{ t('error.noData') }}
        </div>
      </div>

      <!-- 底部操作按钮 -->
      <div class="justify-end gap-2 modal-action">
        <button class="btn btn-ghost" @click="cancelSelection">
          {{ t('button.cancel') }}
        </button>
        <button class="btn btn-primary" @click="confirmSelection">
          {{ t('button.confirm') }} ({{ internalSelected.length }}/{{ maxCount }})
        </button>
      </div>
    </div>
    <form method="dialog" class="modal-backdrop">
      <button>close</button>
    </form>
  </dialog>
</template>

<style lang='scss' scoped></style>
