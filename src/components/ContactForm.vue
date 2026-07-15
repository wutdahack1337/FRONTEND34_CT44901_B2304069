<template>
  <Form @submit="submitContact" :validation-schema="contactFormSchema">
    <div class="form-group">
      <label for="name">Tên</label>
      <Field id="name" name="name" type="text" class="form-control" v-model="contactLocal.name" />
      <ErrorMessage name="name" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="email">E-mail</label>
      <Field id="email" name="email" type="email" class="form-control" v-model="contactLocal.email" />
      <ErrorMessage name="email" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="address">Địa chỉ</label>
      <Field id="address" name="address" type="text" class="form-control" v-model="contactLocal.address" />
      <ErrorMessage name="address" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="phone">Điện thoại</label>
      <Field id="phone" name="phone" type="tel" class="form-control" v-model="contactLocal.phone" />
      <ErrorMessage name="phone" class="error-feedback" />
    </div>

    <div class="form-group form-check">
      <input id="favorite" name="favorite" type="checkbox" class="form-check-input" v-model="contactLocal.favorite" />
      <label for="favorite" class="form-check-label">
        <strong>Liên hệ yêu thích</strong>
      </label>
    </div>

    <div class="form-group">
      <button class="btn btn-primary">Lưu</button>
      <button v-if="contactLocal._id" type="button" class="ms-2 btn btn-danger" @click="deleteContact">
        Xóa
      </button>
      <button type="button" class="ms-2 btn btn-danger" @click="cancel">
        Thoát
      </button>
    </div>
  </Form>
</template>

<script setup>
import { reactive } from "vue";
import { useRouter } from "vue-router";
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

const props = defineProps({
  contact: { type: Object, required: true },
});
const emit = defineEmits(["submit:contact", "delete:contact"]);

const router = useRouter();

// SỬA BUG: clone object thay vì gán reference.
// Tài liệu gốc viết `contactLocal: this.contact` — điều này khiến contactLocal
// và prop `contact` của component cha trỏ chung một object trong bộ nhớ.
// Khi user gõ vào input, v-model sẽ mutate thẳng prop gốc, vi phạm nguyên tắc
// "props chỉ truyền một chiều từ cha xuống con" của Vue.
const contactLocal = reactive({ ...props.contact });

const contactFormSchema = yup.object().shape({
  name: yup
    .string()
    .required("Tên phải có giá trị.")
    .min(2, "Tên phải ít nhất 2 ký tự.")
    .max(50, "Tên có nhiều nhất 50 ký tự."),
  email: yup
    .string()
    .email("E-mail không đúng.")
    .max(50, "E-mail tối đa 50 ký tự."),
  address: yup.string().max(100, "Địa chỉ tối đa 100 ký tự."),
  phone: yup
    .string()
    .matches(/((09|03|07|08|05)+([0-9]{8})\b)/g, "Số điện thoại không hợp lệ."),
});

function submitContact() {
  emit("submit:contact", { ...contactLocal });
}

function deleteContact() {
  emit("delete:contact", contactLocal._id);
}

function cancel() {
  // TODO: thay window.confirm bằng modal component tự viết —
  // giữ tạm ở đây để bám sát luồng logic gốc.
  const reply = window.confirm("Bạn có thay đổi chưa lưu! Bạn có muốn rời khỏi trang?");
  if (!reply) return;
  router.push({ name: "contactbook" });
}
</script>

<style scoped>
@import "@/assets/form.css";
</style>