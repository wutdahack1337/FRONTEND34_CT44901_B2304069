<template>
  <div class="page">
    <h4>Thêm Liên hệ</h4>
    <ContactForm :contact="contact" @submit:contact="createContact" />
  </div>
</template>

<script setup>
import { reactive } from "vue";
import { useRouter } from "vue-router";
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

const router = useRouter();

// Contact rỗng — không có _id nên ContactForm sẽ ở chế độ "add" (không hiện nút Xóa)
const contact = reactive({
  name: "",
  email: "",
  address: "",
  phone: "",
  favorite: false,
});

async function createContact(data) {
  try {
    await ContactService.create(data);
    router.push({ name: "contactbook" });
  } catch (error) {
    console.log(error);
  }
}
</script>