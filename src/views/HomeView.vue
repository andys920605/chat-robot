<script setup>
import { ref, inject } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import InputText from 'primevue/inputtext';
import Button from 'primevue/button';
import ConfirmDialog from 'primevue/confirmdialog';
import 'primeicons/primeicons.css'; 

const faqs = ref([
    { id: 1, question: '目前還在職中嗎？', answer: '是的，目前正在宇泰華科技股份有限公司在職中' },
    // 可以添加更多的 FAQ 項目
]);

// 注入 ConfirmationService
const confirmation = inject('confirmation');

const editingRows = ref([]);

function onRowEditSave(event) {
    const { data, index } = event;
    console.log(data);
    faqs.value[index] = data;
}

function addFAQ() {
    const newId = faqs.value.length ? Math.max(...faqs.value.map(faq => faq.id)) + 1 : 1;
    const newFaq = { id: newId, question: '', answer: '' };
    faqs.value.push(newFaq);
    editingRows.value.push(newFaq);
}

function deleteFAQ(rowData) {
    confirmation.require({
        message: '您確定要刪除此項 FAQ 嗎？',
        header: '確認刪除',
        icon: 'pi pi-exclamation-triangle',
        accept: () => {
            // 從列表中移除 FAQ
            faqs.value = faqs.value.filter(faq => faq.id !== rowData.id);
        },
        reject: () => {
            // 如果取消，則不執行任何操作
        }
    });
}
</script>

<template>
  <h1>機器人 FAQ 設定</h1>
  <!-- 確認彈出視窗 -->
  <ConfirmDialog />
  <DataTable
      v-model:editingRows="editingRows"
      :value="faqs"
      editMode="row"
      dataKey="id"
      @row-edit-save="onRowEditSave"
      :tableStyle="{ minWidth: '50rem' }"
  >
      <Column field="question" header="問題" style="width: 40%">
          <template #editor="{ data, field }">
              <InputText v-model="data[field]" />
          </template>
      </Column>
      <Column field="answer" header="答案" style="width: 40%">
          <template #editor="{ data, field }">
              <InputText v-model="data[field]" />
          </template>
      </Column>
      <Column
          :rowEditor="true"
          style="width: 10%; min-width: 8rem"
          bodyStyle="text-align:center"
      ></Column>
      <!-- 刪除按鈕欄位 -->
      <Column  style="width: 10%; min-width: 8rem">
          <template #body="slotProps">
              <Button
                  icon="pi pi-trash"
                  class="p-button-danger"
                  @click="deleteFAQ(slotProps.data)"
              />
          </template>
      </Column>
  </DataTable>
  <Button id="add-button" label="新增 FAQ" @click="addFAQ" />
</template>

<style scoped>
h1 {
    text-align: center;
}

#add-button {
    display: block;
    margin: 20px auto;
}
</style>
