<template>
  <q-dialog v-model="dialogModel">
    <q-card class="modal-card">
      <q-card-section class="row items-center q-pb-none">
        <div>
          <div class="text-h4 text-weight-bold">{{ user.name }}</div>
          <div class="text-subtitle2 status">{{ user.status }}</div>
        </div>
        <q-space />
        <q-btn icon="close" flat round dense v-close-popup />
      </q-card-section>

      <q-card-section class="modal-content">
        <div class="q-gutter-sm">
          <div class="row">
            <div class="col-4">Date:</div>
            <div class="col-8">{{ user?.registered }}</div>
          </div>
          <div class="row">
            <div class="col-4">Gender:</div>
            <div class="col-8">{{ user?.gender }}</div>
          </div>
          <div class="row">
            <div class="col-4">Country:</div>
            <div class="col-8">{{ user?.country }}</div>
          </div>
          <div class="row">
            <div class="col-4">Email:</div>
            <div class="col-8">{{ user?.email }}</div>
          </div>
        </div>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script setup>

const dialogModel = defineModel()
const props = defineProps({
  user: Object
})

console.log(props)
</script>

<style scoped>
.modal-card {
  min-width: 600px;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 10px;
}

.modal-content {
  font-size: 16px;
  line-height: 1.6;
}

.status {
  color: red;
  font-weight: bold;
}
</style>
