<script setup>
import { ref, computed } from "vue";

const notes = ref([]);
const inputNote = ref("");
const selectAllTitle = ref("Select All");
let eventColor = null;

const addTodo = computed(() => {
  if (inputNote.value.trim().length == 0) {
    alert("Empty Field :(");
    inputNote.value = "";
    return;
  }
  let shownFlag = true;
  if (eventColor !== null && eventColor.target.innerText === "Completed Task") {
    shownFlag = false;
  }

  const newNote = {
    id: Date.now(),
    text: inputNote.value,
    isCompleted: false,
    isEditing: false,
    isShowed: shownFlag,
  };
  notes.value.push(newNote);
  inputNote.value = "";
  checkAllSelected();
});

// Selected Item - Hover
function selectItem(note) {
  note.isCompleted = !note.isCompleted;
}
function selectList(note) {
  note.isCompleted = !note.isCompleted;
  checkAllSelected();
}

// Edit Text on List Item
function editTextOnListItem(note) {
  checkAllSelected();
  if (note.isEditing) {
    if (note.text.trim().length === 0) {
      alert("Empty Field :(");
      note.text = "";
      return;
    }
  }
  note.isEditing = !note.isEditing;
}

// Delete single selected item
function clickedSingleDeleteButton(note) {
  notes.value = notes.value.filter((n) => n.id !== note.id);
  checkAllSelected();
}

// Show All Completed Task
function showCompletedTask() {
  notes.value.forEach((element) => {
    element.isShowed = element.isCompleted;
  });
}

// Show All Remaining Task
function showRemainingTask() {
  notes.value.forEach((element) => {
    element.isShowed = !element.isCompleted;
  });
}

// Show All Task
function showAllTask() {
  notes.value.forEach((element) => (element.isShowed = true));
}

// Delete Completed Task
function clearCompletedTask() {
  notes.value = notes.value.filter((n) => !n.isCompleted);
}

// Add hover effect on button
function hoverButton(event) {
  const buttonID = event.target.id;
  if (buttonID == 1 || buttonID == 5 || buttonID == "") return;

  if (eventColor != null) eventColor.target.style.backgroundColor = "";
  event.target.style.backgroundColor = "lightblue";
  eventColor = event;
}

// Check All item is selected or not
function checkAllSelected() {
  let countTrue = 0,
    countFalse = 0;
  notes.value.forEach((element) =>
    element.isCompleted ? countTrue++ : countFalse++
  );

  if (countFalse == 0 && countTrue) {
    selectAllTitle.value = "Unselect All";
  } else {
    selectAllTitle.value = "Select All";
  }
}

// Select Unselect All items
function selectUnselectAll() {
  let flag = false;
  if (selectAllTitle.value == "Select All") {
    flag = true;
    selectAllTitle.value = "Unselect All";
  } else {
    selectAllTitle.value = "Select All";
  }
  notes.value.forEach((element) => (element.isCompleted = flag));
  updateList(selectAllTitle.value);
}

// Update Complete Remaining List
function updateList(selectedTitle) {
  let buttonId = eventColor.target.id;

  if (buttonId == 2 && selectedTitle == "Unselect All") {
    showCompletedTask();
  } else if (buttonId == 3 && selectedTitle == "Select All") {
    showRemainingTask();
  }
}
</script>

<template>
  <body>
    <div class="optionSectionID" @click="hoverButton">
      <button id="1" @click="selectUnselectAll">
        {{ selectAllTitle }}
      </button>
      <button id="2" @click="showCompletedTask" :class="{ leftMarginE: true }">
        Completed Task
      </button>
      <button id="3" @click="showRemainingTask" :class="{ leftMargin: true }">
        Remaining Task
      </button>
      <button id="4" @click="showAllTask" :class="{ leftMargin: true }">
        Show All
      </button>
      <button id="5" @click="clearCompletedTask" :class="{ leftMarginE: true }">
        Clear Completed
      </button>
    </div>

    <div>
      <label>Note :</label>
      <input
        id="todoTextBox"
        @keyup.enter="addTodo"
        v-model="inputNote"
        placeholder="Enter your text"
        required
      />
      <button id="saveButtonID" @click="addTodo">Save</button><br />
    </div>

    <div id="todoList">
      <li
        v-for="note in notes"
        v-show="note.isShowed"
        :key="note.id"
        :class="note.isCompleted ? 'completed' : 'incompleted'"
        @click="selectList(note)"
      >
        {{ note.text }}

        <button
          id="buttonIsCompleted"
          @click.stop="selectItem(note)"
          :class="{ completed: note.isCompleted }"
        >
          {{ note.isCompleted ? "Completed" : "Incomplete" }}
        </button>

        <button
          id="editBtnID"
          @click.stop()="editTextOnListItem(note)"
          :class="note.isEditing ? 'completed' : 'editColor'"
        >
          {{ note.isEditing ? "Save" : "Edit" }}
        </button>

        <button
          id="deleteBtnID"
          @click.stop()="clickedSingleDeleteButton(note)"
        >
          Delete
        </button>

        <input
          v-if="note.isEditing"
          type="text"
          id="inputEditID"
          v-model="note.text"
          @click.stop
          @keyup.enter="editTextOnListItem(note, $event)"
        />
      </li>
    </div>
  </body>
</template>

<style scoped>
.completed {
  background-color: lightgreen;
}

.incompleted {
  background-color: rgb(168, 196, 224);
}

.editColor {
  background-color: rgb(138, 197, 255);
}

.leftMargin {
  margin-left: 5px;
}

.leftMarginE {
  margin-left: 15px;
}

body {
  display: table;
  margin: auto;
  margin-top: 40px;
}

#todoList {
  position: relative;
}

#todoList li {
  padding-left: 10px;
  margin-bottom: 10px;
  list-style-type: none;
  border-radius: 2px;

  list-style: none;
  padding-right: 40px;
  position: relative;
  height: 28px;
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 86%;
  color: black;
}

#buttonIsCompleted {
  position: absolute;
  top: 50%;
  right: 0px;
  transform: translateY(-50%);
  height: 29px;
}

#deleteBtnID {
  position: absolute;
  top: 50%;
  right: -24%;
  transform: translateY(-50%);
  height: 30px;
  width: 55px;
  background-color: rgb(255, 105, 105);
}

#editBtnID {
  position: absolute;
  top: 50%;
  right: -12%;
  transform: translateY(-50%);
  height: 30px;
  width: 55px;
}

#saveButtonID {
  margin-left: 10px;
  width: 80px;
  background-color: lightgreen;
}

.optionSectionID button {
  width: 120px;
}

#todoTextBox {
  width: 500px;
  height: 35px;
  margin-left: 10px;
  border-radius: 2pt;
  border-color: cornflowerblue;
  border-width: 1pt;
  margin-top: 32px;
  margin-bottom: 14px;
}

#inputEditID {
  position: absolute;
  top: 50%;
  left: 0px;
  transform: translateY(-50%);
  height: 30px;
  width: 552px;
  /* width: 473px; */
  border-radius: 2pt;
  border-color: cornflowerblue;
  border-width: 1pt;
}

button {
  height: 35px;
  border-radius: 4px;
  border-width: 1pt;
  border-color: cornflowerblue;
}
</style>
