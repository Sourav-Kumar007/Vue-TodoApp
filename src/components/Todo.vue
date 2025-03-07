 <!-- Final Code of T0D0 in Vue -->
<script setup>
import { reactive, ref, watch, useTemplateRef, onMounted } from "vue";
const arrayList = reactive([]);
const task = ref("");
const editButtons = ref([]);
const buttonColor = reactive([0, 0, 0, 0, 0]);
const all = ref(false);
const ishow = ref(true);

function allFalse() {
  for (let i = 0; i < 5; i++) {
    buttonColor[i] = 0;
  }
}
function taskAdd() {
  ishow.value = true;
  if (task.value === "") {
    return;
  }
  arrayList.push({
    id: Date.now(),
    task: task.value,
    marked: false,
    isVisible: true,
    editFlag: false,
  });
  task.value = "";
}
const selected = useTemplateRef("allSelect");
function SelectList(item) {
  ishow.value = true;
  item.marked = !item.marked;
  let flag = true;
  for (let j = 0; j < arrayList.length; j++) {
    if (arrayList[j].marked === false) {
      flag = false;
      break;
    }
  }
  all.value = flag;
}

function selectAll() {
  ishow.value = false;
  all.value = !all.value;
  arrayList.forEach(e => {
    e.marked = all.value;
  });
}

function clearCompleted() {
  ishow.value = false;
  const arr = arrayList.filter( e => e.marked === false);
  arrayList.length = 0;
  arrayList.push(...arr);
}
function visible() {
  for (let i = 0; i < arrayList.length; i++) {
    arrayList[i].isVisible = true;
  }
}
function allCompleted() {
  ishow.value = true;
  allFalse();
  buttonColor[0] = 1;
  visible();
  arrayList.forEach(e => e.marked ? e.isVisible = true : e.isVisible = false);
}

function showAll() {
  ishow.value = true;
  allFalse();
  buttonColor[2] = 1;
  visible();
  arrayList.forEach(e => e.isVisible = true);
}

function remaining() {
  ishow.value = true;
  allFalse();
  buttonColor[1] = 1;
  visible();
  arrayList.forEach(e => e.marked ? e.isVisible = false : e.isVisible = true);
}

function edit(item, index) {
  ishow.value = true;
  item.editFlag = !item.editFlag;
}

function deleteItem(index) {
  ishow.value = true;
  arrayList.splice(index, 1);
}
</script>

<template>

  <div class="container">
    <h1 class="heading">Vue Todo</h1>
    <div class="input-task">
      <input type="text" id="input-box" placeholder="Enter a new Task" class="take-input" v-model.trim="task"
        @keyup.enter="taskAdd()" />
      <button class="add-btn" @click="taskAdd()">Add</button>
    </div>
    <ul id="all-task" class="listTask"></ul>

    <div id="all-btn">
      <button id="btn1" @click="allCompleted" :class="buttonColor[0] === 1 ? 'Red' : 'Blue'">
        All Completed
      </button>
      <button @click="remaining()" id="btn2" :class="buttonColor[1] === 1 ? 'Red' : 'Blue'">
        Remaining
      </button>
      <button @click="showAll()" id="btn3" :class="buttonColor[2] === 1 ? 'Red' : 'Blue'">
        Show all
      </button>
      <button @click="clearCompleted()" id="btn4">
        Clear Completed Task
      </button>
      <button id="btn5" @click="selectAll">
        {{all ? 'Unselect All' : 'Select All'}}
      </button>
    </div>
  </div>

  <!-- Output Part  -->
  <ul id="all-task" class="listTask">
    <li v-for="(item, index) in arrayList" v-show="item.isVisible && ishow" :key="item.id" @click="SelectList(item)"
      :class="item.marked === true ? 'Green' : 'White'" id="VueOutput">
      <div class="outPut">
        {{ item.editFlag === false ? item.task : ''}}
        <input v-show="item.editFlag" v-model="item.task" @click.stop />
        <div class="btnOutput">
          <button class="editTask" @click.stop="edit(item, index)" ref="editButtons">
            {{ item.editFlag === false ? 'Edit' : 'Save' }}
          </button>
          <button class="dltTask" @click.stop="deleteItem(index)">
            Delete
          </button>
        </div>
      </div>
    </li>
  </ul>
</template>
<style scoped>
* {
  margin: 0;
  padding: 0;
}

.White {
  background-color: white;
}

.Green {
  background-color: rgb(21, 209, 21);
}
.Red{
  background-color: red !important;
}
.Blue{
  background-color: #4a54e1 !important;
}

#VueOutput {
  margin: 0px auto;
  width: 50%;
}

.container {
  width: 50%;
  margin: 10px auto 15px;
}

.heading {
  margin-left: 330px;
}

.input-task {
  margin-top: 20px;
  max-width: 100%;
}

.take-input {
  width: 80%;
  height: 30px;
}

.add-btn {
  padding: 8px 22px;
  border-radius: 2px;
  background-color: rgb(240, 192, 255);
}

.listTask {
  list-style: none;
  margin-top: 20px;
  padding: 0;
  position: relative;
  width: 100%;
}
.btnOutput{
    position: absolute;
            right: 425px;
}
.listTask li {
  max-width: 86%;
  padding: 10px;
  margin-top: 8px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
  align-items: right;
  border: 1px solid #ddd;
}

.listTask li div {
  width: 10%;
  display: flex;
  justify-content: space-between;
}

.listTask li div button {
  padding: 5px;
}

ul li button{
   display:flex;
   flex-direction: row;
}

#all-btn {
  width: 89%;
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
}

#all-btn button {
  padding: 10px;
  border: none;
  border-radius: 5px;
  background: #5c67f2;
  color: white;
  font-size: 14px;
  cursor: pointer;
  transition: 0.3s;
}

#clear:hover {
  background: #e63939;
}
</style>
