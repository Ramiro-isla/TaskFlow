<script setup>
import NewInput from "./NewInput.vue";

const props = defineProps({
  board: String,
});

const handleNewItem = (text, board) => {
  if (text.trim()) {
    board.items.push({ id: crypto.randomUUID(), title: text.trim() });
  } else {
    errorMessage.value = "Item title cannot be empty";
  }
};

const editItem = (item) => {
  currentItem.value = item;
  modalMode.value = "edit-item";
  openModal.value = true;
};

const handleDeleteItem = (board, itemId) => {
  board.items = board.items.filter((item) => item.id !== itemId);
};

const startDrag = (evt, boardId, itemId) => {
  evt.dataTransfer.effectAllowed = "move";
  evt.dataTransfer.setData("item", JSON.stringify({ boardId, itemId }));
};
</script>
<template>
  <div class="items-container">
    <NewInput @on-new-item="(text) => handleNewItem(text, board)" />
    <div v-if="board.items.length === 0" class="empty-list">
      No items yet. Add a new task.
    </div>
    <div
      class="item"
      v-for="item in board.items"
      :key="item.id"
      draggable="true"
      @dragstart="startDrag($event, board.id, item.id)"
    >
      <input type="text" :value="item.title" readonly />
      <div class="item-actions">
        <button @click="editItem(item)">Edit</button>
        <button class="delete" @click="handleDeleteItem(board, item.id)">
          Delete
        </button>
      </div>
    </div>
  </div>
</template>
<style scoped>
.items-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.empty-list {
  margin-top: auto;
  text-align: center;
  color: #7f8c8d;
  font-style: italic;
}
.item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-radius: 4px;
  margin: 10px 0;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
}

.item input[type="text"] {
  border: none;
  background: none;
  font-size: 1em;
  width: 10rem;
  cursor: default;
  overflow: hidden;
  white-space: nowrap;
  cursor: pointer;
}

.item-actions button {
  border: none;
  border-radius: 4px;
  color: #3498db;
  cursor: pointer;
  margin-left: 5px;
}

.item-actions button.delete {
  color: #e74c3c;
}

.item:hover {
  background-color: #e0e0e0;
}
.empty-list {
  margin: 20px;
  text-align: center;
  color: #7f8c8d;
  font-style: italic;
}
</style>
