<template>
   <div class="p-2">
      <div class="field">
         <div class="control">
            <input
               @input="onInput"
               placeholder="Type here..."
               type="text"
               class="input"
            />
         </div>
      </div>
      <Table
         :content="tableContent"
         :config="tableConfig"
         @select="selectRow"
      />
      <Modal
         :title="modalTitle"
         :show="isModalShown"
         @cancel="deselectRow"
         @save="submitModal"
      >
         <form v-if="editForm" @submit.prevent="submitModal">
            <div class="field has-text-left">
               <label class="label has-text-black">Name</label>
               <div class="control">
                  <input
                     v-model="editForm.name"
                     placeholder="Name"
                     type="text"
                     class="input"
                  />
               </div>
            </div>
            <div class="field has-text-left">
               <div class="select">
                  <select v-model="editForm.status">
                     <option value="verified">Verified</option>
                     <option value="unverified">Unverified</option>
                  </select>
               </div>
            </div>
         </form>
      </Modal>
      <HiddenMessage />
   </div>
</template>
<script>
import Table from '@/components/Table.vue'
import Modal from '@/components/Modal.vue'
import HiddenMessage from '@/components/HiddenMessage.vue'
import { computed, onMounted, reactive, watch } from 'vue'
import { filterList, mapList } from './listHelper'
import dummy from '@/assets/dummy.json'
import timeout from 'q'
export default {
   components: { Table, Modal, HiddenMessage },
   setup() {
      const tableConfig = {
         columns: [
            { key: 'id', header: 'ID' },
            { key: 'name', header: 'Name' },
            { key: 'cords_display', header: 'Cords' },
            { key: 'tags_display', header: 'Tags' },
            { key: 'status', header: 'Status' }
         ]
      }
      const state = reactive({
         items: [],
         initLoading: true,
         search: '',
         timeout,
         selectedRow: null
      })

      const editForm = reactive({
         name: '',
         status: ''
      })

      const tableContent = computed(() =>
         state.items.filter(item => filterList(item, state.search)).map(mapList)
      )

      const selectRow = row => {
         state.selectedRow = row
      }

      const deselectRow = () => {
         state.selectedRow = null
      }

      const onInput = ({ target: { value } }) => {
         clearTimeout(timeout)
         state.timeout = setTimeout(() => (state.search = value), 500)
      }

      const submitModal = () => {
         const index = state.items.findIndex(
            item => item.id === state.selectedRow.id
         )

         state.items[index] = {
            ...state.items[index],
            name: editForm.name,
            status: editForm.status
         }
         state.items = [...state.items]
         state.selectedRow = null
      }

      watch(
         () => state.selectedRow,
         () => {
            editForm.name = state.selectedRow?.name ?? ''
            editForm.status = state.selectedRow?.status ?? ''
         }
      )

      const modalTitle = computed(() => {
         return `Edit ${state.selectedRow?.name}`
      })

      const isModalShown = computed(() => {
         return !!state.selectedRow
      })

      const mockRequest = () => {
         return new Promise(resolve => {
            setTimeout(() => {
               state.items = dummy
               resolve()
            }, 500)
         })
      }
      onMounted(async () => {
         await mockRequest()
         state.loading = false
      })
      return {
         tableContent,
         tableConfig,
         onInput,
         state,
         selectRow,
         deselectRow,
         modalTitle,
         isModalShown,
         editForm,
         submitModal
      }
   }
}
</script>
