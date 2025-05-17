<template>
  <div class="q-pb-xl blue-bar-container">
    <img src="../assets/Avatar.png" alt="User Image" class="bar-image" />
    <div class="blue-bar row items-center q-pl-xl q-gutter-sm">
      <div class="column text-white q-mr-xl">
        <div class="text-h3 text-weight-bold">John Doe</div>
        <div class="text-subtitle2 text-weight-regular">Last login: 17 May 2025</div>
      </div>
      <q-btn icon="send" label="Send Message" color="white" text-color="primary" />
      <q-btn icon="add" label="Add Friend" color="white" text-color="primary" />
    </div>
  </div>

  <div class="q-px-xl q-mx-auto" style="max-width: 1500px;">
    <div class="q-pa-xl">
      <q-input
      v-model="searchQuery"
      label="Search users..."
      outlined
      dense
      clearable
      class="q-mb-md"
      @keyup.enter="onSearch"
    />
    <div v-if="filteredUserList.length === 0" class="text-center q-my-md text-grey">
      No users found.
    </div>
      <q-card flat>
        <div class="row text-bold q-pa-sm font-color">
          <div class="col-2">Date</div>
          <div class="col-3">Name</div>
          <div class="col-1">Gender</div>
          <div class="col-3">Country</div>
          <div class="col-3">Email</div>
        </div>

        <div class="q-gutter-md">
          <q-card
            v-for="user in filteredUserList"
            :key="user.email"
            class="row q-pa-sm items-center user-card"
            @click="openUserDialog(user)"
          >
            <div class="col-2">{{ user.registered }}</div>
            <div class="col-3 text-bold">{{ user.name }}</div>
            <div class="col-1">{{ user.gender }}</div>
            <div class="col-3">{{ user.country }}</div>
            <div class="col-3">{{ user.email }}</div>
          </q-card>
        </div>
      </q-card>

      <div class="q-pt-md text-center">
        <q-btn
          label="Refresh"
          color="primary"
          icon="refresh"
          :loading="loading"
          @click="refreshUserList"
        />
      </div>
    </div>
  </div>
  <UserDialog
  v-model="dialogVisible"
  :user="selectedUser"
/>
</template>


<script setup>
import { ref, onMounted } from 'vue'
import UserDialog from '../components/UserDialog.vue'
import axios from 'axios'

const apiBaseUrl = import.meta.env.VITE_API_BASE_URL
const loading = ref(false)
const userList = ref([])
const selectedUser = ref(null)
const dialogVisible = ref(false)
const searchQuery = ref('')
const filteredUserList = ref([])
const pagination = ref({
  sortBy: "desc",
  descending: false,
  page: 1,
  rowsPerPage: 20,
  rowsNumber: 0
})

const openUserDialog = (user) => {
  selectedUser.value = user
  dialogVisible.value = true
}

const getUserList = async (props) => {
  const { page, rowsPerPage } = props.pagination
  loading.value = true
  try {
    const response = await axios.get(`${apiBaseUrl}?page=${page}&results=${rowsPerPage}`)

    const results = response.data?.results

    if (Array.isArray(results)) {
      userList.value = results.map(user => ({
        name: `${user.name.first} ${user.name.last}`,
        gender: user.gender.toUpperCase(),
        email: user.email,
        country: user.location.country,
        registered: new Intl.DateTimeFormat('en-GB', {
          day: 'numeric',
          month: 'short',
          year: 'numeric'
        }).format(new Date(user.registered.date))
      }))
    } else {
      userList.value = []
      console.warn('No users found in API response')
    }

    pagination.value = {
      ...pagination.value,
      page: response.data?.info?.page || 1,
      rowsNumber: response.data?.info?.results || 0
    }

  } catch (error) {
    console.error('Error fetching user:', error)
  } finally {
    loading.value = false
  }
}


const onSearch = () => {
  const lowerSearch = searchQuery.value.toLowerCase()
  filteredUserList.value = userList.value.filter(user =>
    user.name.toLowerCase().includes(lowerSearch) ||
    user.email.toLowerCase().includes(lowerSearch) ||
    user.country.toLowerCase().includes(lowerSearch) ||
    user.gender.toLowerCase().includes(lowerSearch)
  )
}

const refreshUserList = async () => {
  await getUserList({ pagination: pagination.value })
  filteredUserList.value = userList.value
}


onMounted(() => {
  getUserList({ pagination: pagination.value }).then(() => {
    filteredUserList.value = userList.value
  })
})

</script>
<style lang="css" scoped>
.blue-bar-container {
  position: relative;
  width: 100%;
}

.blue-bar {
  background-color: #007bff;
  height: 150px;
  width: 100%;
  display: flex;
  align-items: center;
  padding-left: 250px;
}

.bar-image {
  position: absolute;
  top: 50px;
  left: 40px;
  width: 180px;
  height: 180px;
  border-radius: 15px;
  border: 5px solid white;
  z-index: 10;
}

.font-color {
  color: grey;
}

.user-card {
  min-height: 80px;
  display: flex;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ddd;
  border-radius: 4px;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.05);
}

</style>
