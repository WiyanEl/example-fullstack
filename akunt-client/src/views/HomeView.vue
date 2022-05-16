<script>
import { RouterLink } from "vue-router";
import { onMounted, ref } from "vue";
import axios from "axios";

export default {
  setup() {
    let transactions = ref([]);

    onMounted(() => {
      axios
        .get("http://127.0.0.1:8000/api/transaction")
        .then((response) => {
          transactions.value = response.data;
        })
        .catch((err) => {
          console.log(err.response);
        });
    });

    function destroy(id, index) {
      axios
        .delete(`http://127.0.0.1:8000/api/transaction/${id}`)
        .then(() => {
          transactions.value.data.splice(index, 1);
        })
        .catch((err) => {
          console.log(err.response.data);
        });
    }

    return {
      transactions,
      destroy,
    };
  },
};
</script>

<template>
  <div class="container my-5">
    <div class="row mb-3">
      <div class="col">
        <router-link :to="{ name: 'create' }" class="btn btn-sm btn-primary"><i class="bi bi-plus-lg"></i> Add</router-link>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="card">
          <h5 class="card-header">Transaction List</h5>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th>#</th>
                    <th>Title</th>
                    <th>Amount</th>
                    <th>Type</th>
                    <th>Action</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(transaction, index) in transactions.data" :key="transaction.id">
                    <th>{{ index + 1 }}</th>
                    <td>{{ transaction.title }}</td>
                    <td>Rp. {{ transaction.amount }}</td>
                    <td>{{ transaction.type }}</td>
                    <td>
                      <router-link :to="{ name: 'edit', params: { id: transaction.id } }" class="btn btn-sm btn-success me-1"><i class="bi bi-pencil-square"></i></router-link>
                      <button class="btn btn-sm btn-danger" @click="destroy(transaction.id, index)"><i class="bi bi-trash"></i></button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
