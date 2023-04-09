<script setup>
import { storeToRefs } from 'pinia';

import { useUsersStore } from '@/stores';

const usersStore = useUsersStore();
const { users } = storeToRefs(usersStore);

usersStore.getAll();
</script>

<template>
    <h1>Home Page</h1>
    <router-link to="/users/add" class="btn btn-sm btn-success mb-2">Add Test</router-link>
    <div class="mb-2">
      <label for="search">Search:</label>
      <input type="text" v-model="search" id="search" class="form-control" placeholder="Search by name or address">
    </div>
    <table class="table table-striped">
        <thead>
            <tr>
                <th style="width: 30%">Nama</th>
                <th style="width: 30%">Alamat</th>
                <th style="width: 30%">Limit Kredit</th>
                <th style="width: 10%"></th>
            </tr>
        </thead>
        <tbody>
            <template v-if="filteredUsers.length">
                <tr v-for="user in filteredUsers" :key="user.id">
                    <td>{{ user.username }}</td>
                    <td>{{ user.firstName }}</td>
                    <td>{{ formatCurrency(user.lastName) }}</td> 
                    <td style="white-space: nowrap">
                        <router-link :to="`/users/edit/${user.id}`" class="btn btn-sm btn-primary mr-1">Edit</router-link>
                        <button @click="usersStore.delete(user.id)" class="btn btn-sm btn-danger btn-delete-user" :disabled="user.isDeleting">
                            <span v-if="user.isDeleting" class="spinner-border spinner-border-sm"></span>
                            <span v-else>Delete</span>
                        </button>
                    </td>
                </tr>
            </template>
            <tr v-if="!filteredUsers.length && users.loading">
                <td colspan="4" class="text-center">
                    <span class="spinner-border spinner-border-lg align-center"></span>
                </td>
            </tr>
            <tr v-if="!filteredUsers.length && users.error">
                <td colspan="4">
                    <div class="text-danger">Error loading users: {{users.error}}</div>
                </td>
            </tr> 
            <tr v-if="!filteredUsers.length && !users.loading && !users.error">
          <td colspan="4">
            <div class="text-muted text-center">No users found.</div>
          </td>
        </tr>           
        </tbody>
    </table>
</template>

<script>
export default {
    data() {
    return {
      search: '',
    };
  },
  computed: {
    filteredUsers() {
      const searchQuery = this.search.trim().toLowerCase();
      if (!searchQuery) return this.users;

      return this.users.filter(user => {
        const { username, firstName } = user;
        return (
          username.toLowerCase().includes(searchQuery) ||
          firstName.toLowerCase().includes(searchQuery)
        );
      });
    },
  },
  methods: {
    formatCurrency(value) {
      const formatter = new Intl.NumberFormat('id-ID', {
        style: 'currency',
        currency: 'IDR',
        minimumFractionDigits: 2
      });
      return formatter.format(parseFloat(value));
    }
  }
};
</script>
