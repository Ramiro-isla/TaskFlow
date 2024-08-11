<script setup>
import { ref, defineEmits } from "vue";

const newItemText = ref("");
const emit = defineEmits(["on-new-item"]);

const addItem = () => {
  if (newItemText.value.trim()) {
    emit("on-new-item", newItemText.value.trim());
    newItemText.value = "";
  } else {
    alert("Item title cannot be empty");
  }
};
</script>

<template>
  <div class="new-input">
    <input v-model="newItemText" type="text" placeholder="Enter new item" />
    <button @click="addItem">
      <img
        src="../assets/icon/add-button.png"
        class="add-button"
        alt="add button"
      />
    </button>
  </div>
</template>

<style scoped>
.new-input {
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.new-input input[type="text"] {
  padding: 10px;
  border-radius: 4px;
  width: 11rem;
  border: 1px solid #ccc;
}

.new-input button {
  border: none;
  background-color: transparent;
  cursor: pointer;
}
.add-button {
  width: 30px;
}
.new-input button:hover {
  opacity: 0.8;
}
</style>
