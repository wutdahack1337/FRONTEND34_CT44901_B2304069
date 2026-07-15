<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>

    <div class="mt-3 col-md-6">
      <h4>Danh bạ <i class="fas fa-address-book"></i></h4>

      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />
      <p v-else>Không có liên hệ nào.</p>

      <div class="mt-3 row justify-content-around align-items-center">
        <button class="btn btn-sm btn-primary" @click="refreshList">
          <i class="fas fa-redo"></i> Làm mới
        </button>
        <button class="btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Thêm mới
        </button>
        <button class="btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xóa tất cả
        </button>
      </div>
    </div>

    <div class="mt-3 col-md-6">
      <div v-if="activeContact">
        <h4>Chi tiết Liên hệ <i class="fas fa-address-card"></i></h4>
        <ContactCard :contact="activeContact" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from "vue";
import { useRouter } from "vue-router";
import ContactCard from "@/components/ContactCard.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactList from "@/components/ContactList.vue";
import ContactService from "@/services/contact.service";

const router = useRouter();

const contacts = ref([]);
const activeIndex = ref(-1);
const searchText = ref("");

// Bỏ chọn phần tử đang chọn khi từ khóa tìm kiếm thay đổi
watch(searchText, () => {
  activeIndex.value = -1;
});

// Chuyển các đối tượng contact thành chuỗi để tiện tìm kiếm
const contactStrings = computed(() =>
  contacts.value.map((c) => [c.name, c.email, c.address, c.phone].join(""))
);

const filteredContacts = computed(() => {
  if (!searchText.value) return contacts.value;
  return contacts.value.filter((_c, index) =>
    contactStrings.value[index].includes(searchText.value)
  );
});

const activeContact = computed(() => {
  if (activeIndex.value < 0) return null;
  return filteredContacts.value[activeIndex.value];
});

const filteredContactsCount = computed(() => filteredContacts.value.length);

async function retrieveContacts() {
  try {
    contacts.value = await ContactService.getAll();
  } catch (error) {
    console.log(error);
  }
}

function refreshList() {
  retrieveContacts();
  activeIndex.value = -1;
}

async function removeAllContacts() {
  if (!confirm("Bạn muốn xóa tất cả Liên hệ?")) return;
  try {
    await ContactService.deleteAll();
    refreshList();
  } catch (error) {
    console.log(error);
  }
}

function goToAddContact() {
  router.push({ name: "contact.add" });
}

onMounted(refreshList);
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>