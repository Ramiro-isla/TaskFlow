<script setup>
import { reactive, ref } from "vue";
import NewInput from "./NewInput.vue";
import ModalComponent from "./ModalComponent.vue";

const boards = reactive([
  { id: crypto.randomUUID(), name: "Board 1", items: [] },
  { id: crypto.randomUUID(), name: "Board 2", items: [] },
]);

const openModal = ref(false);
const errorMessage = ref("");
const modalMode = ref("");
const currentBoard = ref(null);
const currentItem = ref(null);

const handleNewItem = (text, board) => {
  if (text.trim()) {
    board.items.push({ id: crypto.randomUUID(), title: text.trim() });
  } else {
    errorMessage.value = "Item title cannot be empty";
  }
};

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

const editItem = (item) => {
  currentItem.value = item;
  modalMode.value = "edit-item";
  openModal.value = true;
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

const handleDeleteItem = (board, itemId) => {
  board.items = board.items.filter((item) => item.id !== itemId);
};

const startDrag = (evt, boardId, itemId) => {
  evt.dataTransfer.effectAllowed = "move";
  evt.dataTransfer.setData("item", JSON.stringify({ boardId, itemId }));
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

/* Contenedor de tableros */
.boards-container {
  display: flex;
  flex-wrap: wrap;
  padding: 20px;
  gap: 20px;
}

.board {
  background-color: #ffffff;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  width: 300px;
}

.board-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.board-header h3 {
  margin: 0;
  font-size: 1.2em;
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

/* Estilos de ítems */
.item {
  padding: 10px;
  border-radius: 4px;
  margin: 10px 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
}

.item input[type="text"] {
  border: none;
  background: none;
  font-size: 1em;
  width: 11rem;
  cursor: default; 
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
