<script setup>
import { reactive, ref } from "vue";
import ModalComponent from "./ModalComponent.vue";
import ItemComponent from "./ItemComponent.vue";

const boards = reactive([
  { id: crypto.randomUUID(), name: "Board 1", items: [] },
  { id: crypto.randomUUID(), name: "Board 2", items: [] },
]);

const openModal = ref(false);
const errorMessage = ref("");
const modalMode = ref("");
const currentBoard = ref(null);
const currentItem = ref(null);

const handleNewBoard = () => {
  modalMode.value = "create-board";
  openModal.value = true;
};

const createBoard = (name) => {
  if (!name.trim()) {
    errorMessage.value = "Board name is required";
    return;
  }
  boards.push({
    id: crypto.randomUUID(),
    name: name.trim(),
    items: [],
  });
  closeModal();
};

const editBoard = (board) => {
  currentBoard.value = board;
  modalMode.value = "edit-board";
  openModal.value = true;
};

const updateBoardName = (newName) => {
  if (!newName.trim()) {
    errorMessage.value = "Board name cannot be empty";
    return;
  }
  currentBoard.value.name = newName.trim();
  closeModal();
};

const updateItemName = (newTitle) => {
  if (!newTitle.trim()) {
    errorMessage.value = "Item title cannot be empty";
    return;
  }
  currentItem.value.title = newTitle.trim();
  closeModal();
};

const closeModal = () => {
  openModal.value = false;
  errorMessage.value = "";
  currentBoard.value = null;
  currentItem.value = null;
};

const handleDeleteBoard = (boardId) => {
  const index = boards.findIndex((board) => board.id === boardId);
  if (index !== -1) {
    boards.splice(index, 1);
  }
};

const onDrop = (evt, dest) => {
  const { boardId, itemId } = JSON.parse(evt.dataTransfer.getData("item"));
  const sourceBoard = boards.find((board) => board.id === boardId);
  const item = sourceBoard.items.find((item) => item.id === itemId);

  sourceBoard.items = sourceBoard.items.filter((i) => i.id !== item.id);
  dest.items.push({ ...item });
};
</script>

<template>
  <nav>
    <ul>
      <li><a href="#" @click.prevent="handleNewBoard">Create Board</a></li>
    </ul>
  </nav>

  <ModalComponent
    v-if="openModal"
    :isOpen="openModal"
    :boardName="modalMode === 'edit-board' ? currentBoard?.name : ''"
    :itemName="modalMode === 'edit-item' ? currentItem?.title : ''"
    :mode="modalMode"
    :onSubmit="
      modalMode === 'create-board'
        ? createBoard
        : modalMode === 'edit-board'
        ? updateBoardName
        : updateItemName
    "
    :onCancel="closeModal"
    :errorMessage="errorMessage"
  />

  <div class="boards-container">
    <div
      class="board"
      v-for="board in boards"
      :key="board.id"
      @drop="onDrop($event, board)"
      @dragover.prevent
      @dragenter.prevent
    >
      <header class="board-header">
        <h3>{{ board.name }}</h3>
        <div class="board-actions">
          <button @click="editBoard(board)">Edit</button>
          <button class="delete" @click="handleDeleteBoard(board.id)">
            Delete
          </button>
        </div>
      </header>
      <ItemComponent :board="board" />
    </div>
    <div v-if="boards.length === 0" class="empty-list">
      No boards yet, add a new board.
    </div>
  </div>
</template>

<style scoped>
nav {
  padding: 20px;
}

nav ul {
  list-style: none;
  padding-left: 0;
}

nav a {
  background-color: #2e7dff;
  color: whitesmoke;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-decoration: none;
  transition: all 0.3s ease;
}

nav a:hover {
  text-decoration: underline;
}

.boards-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.board {
  width: 18rem;
  display: flex;
  flex-direction: column;
  background-color: #ffffff;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  margin: 1rem;
}

.board-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.board-header h3 {
  margin: 0;
  font-size: 1.3em;
  width: 8rem;
  overflow: hidden;
  white-space: nowrap;
  cursor: pointer;
}

.board-header h3:hover {
  overflow: auto;
}

.board-actions button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
  margin-left: 5px;
}

.board-actions button.delete {
  background-color: #e74c3c;
}

.board-actions button:hover {
  opacity: 0.8;
}

.empty-list {
  margin: 20px;
  text-align: center;
  color: #7f8c8d;
  font-style: italic;
}
</style>
