<script setup>
import { ref, watch } from "vue";

const props = defineProps({
  isOpen: Boolean,
  boardName: String,
  itemName: String,
  mode: String,
  onSubmit: Function,
  onCancel: Function,
  errorMessage: String,
});

const inputText = ref(props.boardName || props.itemName || "");
const localErrorMessage = ref("");

watch(
  () => props.isOpen,
  () => {
    inputText.value = props.boardName || props.itemName || "";
    localErrorMessage.value = "";
  }
);

const handleSubmit = () => {
  if (!inputText.value.trim()) {
    localErrorMessage.value = "Field cannot be empty";
  } else {
    props.onSubmit(inputText.value.trim());
  }
};
</script>

<template>
  <div v-if="isOpen" class="modal-backdrop">
    <div class="modal">
      <h3>
        {{
          mode === "edit-board"
            ? "Edit Board"
            : mode === "edit-item"
            ? "Edit Item"
            : "Create Board"
        }}
      </h3>
      <input v-model="inputText" type="text" />
      <div v-if="localErrorMessage || errorMessage" class="error">
        {{ localErrorMessage || errorMessage }}
      </div>
      <button class="cancel-button" @click="onCancel">Cancel</button>
      <button class="save-button" @click="handleSubmit">Save</button>
    </div>
  </div>
</template>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.2);
  width: 400px;
  padding: 50px;
  text-align: center;
}

.modal h3 {
  margin-top: 0;
}

.modal input[type="text"] {
  width: calc(100% - 20px);
  padding: 10px;
  margin: 15px 0;
  border-radius: 4px;
  border: 1px solid #ccc;
}

.modal .error {
  color: #f44336;
  margin-bottom: 15px;
  font-size: 0.9em;
}

.save-button {
  background-color: #2e7dff;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 10px;
}

.cancel-button {
  background-color: transparent;
  padding: 10px 15px;
  border: 1px solid rgb(192, 192, 192);
  border-radius: 4px;
  cursor: pointer;
  margin-right: 10px;
}

.modal button:hover {
  opacity: 0.8;
}
</style>
