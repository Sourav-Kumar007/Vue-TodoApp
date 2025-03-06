<script setup>
import { reactive, ref, watch, useTemplateRef, onMounted } from 'vue';
const arrayList = reactive([]);
const task = ref("");
const editButtons = ref([]);
const editVal = ref("");
function taskAdd() {
  if (task.value === "") {
    return;
  }
  arrayList.push({
    id: Date.now(),
    task: task.value,
    marked: false,
    backgroundColor: "#FFFFFF",
    isVisible: true,
    editFlag : false
  });
  task.value = "";
}
const selected = useTemplateRef('allSelect');

function SelectList(item) {
  item.backgroundColor = "#C0E49A";
  item.marked = true;
  let flag = true;
  for (let j = 0; j < arrayList.length; j++) {
    if (arrayList[j].marked === false) {
      flag = false;
      break;
    }
  }
  if (flag) {
    selected.value.innerText = 'Unselect All';
  }
  else {
    selected.value.innerText = 'Select All';
  }
}

function selectAll() {
  let flag = true;
  if (selected.value.innerText === 'Select All') {
    selected.value.innerText = 'Unselect All';
  }
  else {
    selected.value.innerText = 'Select All';
    flag = false;
  }
  for (let i = 0; i < arrayList.length; i++) {
    arrayList[i].marked = flag;
    if (flag) {
      arrayList[i].backgroundColor = "#C0E49A";
    }
    else {
      arrayList[i].backgroundColor = "#FFFFFF";
    }
  }
}

function clearCompleted() {
  console.log('yes');
  while (1) {
    let idx = -1;
    for (let i = 0; i < arrayList.length; i++) {
      if (arrayList[i].marked) {
        idx = i;
        break;
      }
    }
    if (idx == -1) {
      break;
    }
    console.log(idx);
    arrayList.splice(idx, 1);
  }
}
function visible() {
  for (let i = 0; i < arrayList.length; i++) {
    arrayList[i].isVisible = true;
  }
}
function allCompleted() {
  visible();
  for (let i = 0; i < arrayList.length; i++) {
    if (arrayList[i].marked === false) {
      arrayList[i].isVisible = false;
    }
  }
}

function showAll() {
  visible();
  for (let i = 0; i < arrayList.length; i++) {
    if (arrayList[i].marked === false) {
      arrayList[i].isVisible = true;
    }
  }
}

function remaining() {
  visible();
  for (let i = 0; i < arrayList.length; i++) {
    if (arrayList[i].marked === true) {
      arrayList[i].isVisible = false;
    }
  }
}

function edit(item, index) {
  if (editButtons.value[index]) {
    if (editButtons.value[index].innerText === "Edit") {
      editButtons.value[index].innerText = "Save";
      item.editFlag = true;
      editVal.value = item.task;
      item.task = "";
    }
    else{
      editButtons.value[index].innerText = "Edit";
      item.task = editVal.value;
      item.editFlag = false;
    }
  }
}

function deleteItem(index){
  arrayList.splice(index , 1);
}
</script>

<template>
  <div class="container">
    <h1 class="heading">Todo App</h1>
    <div class="input-task">
      <input type="text" id="input-box" placeholder="Enter a new Task" class="take-input" v-model.trim="task"
        @keyup.enter="taskAdd()">
      <button class="add-btn" @click="taskAdd()">Add</button>
    </div>
    <ul id="all-task" class="listTask"></ul>

    <div id="all-btn">
      <button id="btn1" @click="allCompleted"> All Completed </button>
      <button @click="remaining()" id="btn2"> Remaining </button>
      <button @click="showAll()" id="btn3"> Show all </button>
      <button @click="clearCompleted()" id="btn4"> Clear Completed Task</button>
      <button id="btn5" ref="allSelect" @click="selectAll"> Select All </button>
    </div>
  </div>


  <!-- Output Part  -->
  <ul id="all-task" class="listTask">
    <li v-for="(item, index) in arrayList" v-show="item.isVisible" :key="item.id" @click="SelectList(item)"
      :style="{ backgroundColor: item.backgroundColor }" id="VueOutput">
      <div>
        {{ item.task }}
        <input v-show="item.editFlag" v-model="editVal" @click.stop>
        <div>
          <button class="editTask" @click.stop="edit(item, index)" ref="editButtons"> Edit </button>
          <button class="dltTask" @click.stop="deleteItem(index)"> Delete </button>
        </div>
      </div>
    </li>
  </ul>


</template>
<style scoped>
*{
    margin : 0;
    padding : 0;
}

.container{
    width : 50%;
    margin : 10px auto 15px;
}
.heading{
    margin-left:330px;
}
.input-task{
    margin-top  : 20px;
    max-width:100%;
}
.take-input{
    width:80%;
    height: 30px;
}
.add-btn{
    padding:8px 22px;
    border-radius:2px;
    background-color: rgb(240, 192, 255);
}

.listTask {
    list-style: none;
    margin-top: 20px;
    padding: 0;
}

.listTask li {
    max-width:86%;
    background: #fff;
    padding: 10px;
    margin-top: 8px;
    border-radius: 5px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border: 1px solid #ddd;
    transition: 0.3s;
}
.listTask li div{
    width:12%;
    display:flex;
    justify-content: space-between;
}
.listTask li div button{

    padding:5px;
}


.listTask li:hover {
    background: #f9f9f9;
}

ul li button{
   display:flex;
   flex-direction: row;
}
#all-btn {
    width:89%;
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

#all-btn button:hover {
    background: #4a54e1;
}
#clear:hover {
    background: #e63939;
}
</style>
