<script>

import { initializeApp, getApps,getApp } from "firebase/app";

import { getFirestore,collection,onSnapshot,  doc, addDoc, deleteDoc, updateDoc } from "firebase/firestore";

import {firebaseConfig} from "$lib/fbConfig";



let firebaseApp = initializeApp(firebaseConfig);
getApp();
let db = getFirestore();
let colRef= collection(db, "todos");


let todos = [];
onSnapshot(colRef, (querySnapshot) => {
    let fbTodos=[];
  querySnapshot.forEach((doc) => {
    let todo= {...doc.data(), id: doc.id};
    fbTodos = [todo, ...fbTodos];
  });
  todos= fbTodos;
});


  
    let task = "";
    let error = "";
  
    const addTodo = async () => {
    if (task !== "") {
      const docRef = await addDoc(collection(db, "todos"), {
        task: task,
        isComplete: false,
        createdAt: new Date(),
      });
      error = "";
    } else {
      error = "Task empty";
    }
    task = "";
  };
  
  const markTodoAsComplete = async (item) => {
    await updateDoc(doc(db, "todos", item.id), {
      isComplete: !item.isComplete,
    });
  };

  const deleteTodo = async (id) => {
    await deleteDoc(doc(db, "todos", id));
  };

  const keyIsPressed = (event) => {
    if (event.key === "Enter") {
      addTodo();
    }
  };
  </script>
  <link rel="stylesheet" href="https://cdn.simplecss.org/simple.min.css" >

  <br>  <br>  <br>
  <input type="text" placeholder="Add task" bind:value={task} />
  <button on:click={addTodo}>Add</button>
  
  <ol>
  
    {#each todos as item}
      <li class:complete={item.isComplete}>
        <span>
          {item.task}
        </span>
        <span>
          <button on:click={() => markTodoAsComplete(item)}>✔</button>
          <button on:click={() => deleteTodo(item.id)}>✘</button>
        </span>
      </li>
    {:else}
      <p>No todos found</p>
    {/each}
    <p class="error">{error}</p>
  </ol>
  
  <svelte:window on:keydown={keyIsPressed} />
  
  <style>
    
    .complete {
      text-decoration: line-through;
    }
    .error {
      color: red;
    }
  </style>