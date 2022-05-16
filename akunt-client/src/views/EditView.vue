<script>
import { onMounted } from "vue";
import { reactive, ref } from "@vue/reactivity";
import axios from "axios";
import { RouterLink, useRouter, useRoute } from "vue-router";

export default {
  setup() {
    let transactions = reactive({
      title: "",
      amount: "",
      time: "",
      type: "",
    });

    const validation = ref([]);

    const router = useRouter();
    const route = useRoute();

    onMounted(() => {
      axios
        .get(`http://127.0.0.1:8000/api/transaction/${route.params.id}`)
        .then((response) => {
          transactions.title = response.data.title;
          transactions.amount = response.data.amount;
          transactions.time = response.data.time;
          transactions.type = response.data.type;
        })
        .catch((err) => {
          console.log(err.response.data);
        });
    });

    function update() {
      axios
        .put(`http://127.0.0.1:8000/api/transaction/${route.params.id}`, this.transactions)
        .then(() => {
          router.push({
            name: "home",
          });
        })
        .catch((err) => {
          validation.value = err.response.data;
        });
    }

    return {
      transactions,
      validation,
      router,
      update,
    };
  },
};
</script>

<template>
  <div class="container my-5">
    <div class="row mb-3">
      <div class="col">
        <router-link :to="{ name: 'home' }" class="btn btn-sm btn-primary"><i class="bi bi-arrow-left"></i> Back</router-link>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="card">
          <h5 class="card-header">Edit Transaction</h5>
          <div class="card-body">
            <form action="" v-on:submit.prevent>
              <div class="mb-3">
                <label for="title" class="form-label">Title</label>
                <input v-model="transactions.title" type="text" id="title" class="form-control" />
                <div v-if="validation.title" class="text-danger">
                  {{ validation.title[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label for="amount" class="form-label">Amount</label>
                <input v-model="transactions.amount" type="number" id="amount" class="form-control" />
                <div v-if="validation.amount" class="text-danger">
                  {{ validation.amount[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label for="timestamp" class="form-label">Time</label>
                <input v-model="transactions.time" type="text" id="timestamp" class="form-control" placeholder="yyyy-mm-dd hh:mm:ss" />
                <div v-if="validation.time" class="text-danger">
                  {{ validation.time[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label for="type" class="form-label">Type</label>
                <select v-model="transactions.type" id="type" class="form-select">
                  <option value="expense">Expense</option>
                  <option value="revenue">Revenue</option>
                </select>
                <div v-if="validation.type" class="text-danger">
                  {{ validation.type[0] }}
                </div>
              </div>
              <div class="mb-3">
                <button type="submit" class="btn btn-success" @click="update">Create</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
