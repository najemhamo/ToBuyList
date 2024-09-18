<script setup>
// ref : to handle the state
// onMounted : to handle the local storge when the page is reloaded
// computed : to handle computed properties
// watch : will watch the changes in the state
import { ref, onMounted, computed, watch } from "vue";

const toBuyItems = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const ItemsList_asc = computed(() =>
  toBuyItems.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

// To add the name to the local storage
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  toBuyItems,
  (newVal) => {
    localStorage.setItem("toBuyItems", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addToBuyList = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  toBuyItems.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

const removeToBuyItem = (item) => {
  toBuyItems.value = toBuyItems.value.filter((t) => t !== item);
};

// To save the data when the page is reloaded
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  toBuyItems.value = JSON.parse(localStorage.getItem("toBuyItems")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-tobuy">
      <h3>CREATE A TO BUY LIST</h3>

      <form id="new-tobuy-form" @submit.prevent="addToBuyList">
        <h4>What's on your to buy list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. apples, bananas, etc."
          v-model="input_content"
        />

        <h4>Pick a priority</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="high"
              v-model="input_category"
            />
            <span class="bubble high"></span>
            <div>High</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="low"
              v-model="input_category"
            />
            <span class="bubble low"></span>
            <div>Low</div>
          </label>
        </div>

        <input type="submit" value="Add new item" />
      </form>
    </section>

    <section class="tobuy-list">
      <h3>TO BUY LIST</h3>
      <div class="list" id="tobuy-list">
        <div
          v-for="item in ItemsList_asc"
          :class="`tobuy-item ${item.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="item.done" />
            <span
              :class="`bubble ${item.category == 'high' ? 'high' : 'low'}`"
            ></span>
          </label>

          <div class="tobuy-content">
            <input type="text" v-model="item.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeToBuyItem(item)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
