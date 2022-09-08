<template>
  <div v-if="!user" class="w-full flex justify-center transition-opacity" >
    <img  src="../assets/images/logo-512x512.png" class="" alt="logo">
  </div>

  <div v-if="user" class="max-w-screen-md mx-auto px-4 py-10">
    <div
      class="w-full flex justify-center items-center shadow-md cursor-pointer mb-5 p-5"
      v-for="(tasks, index) in data"
      :key="index"
    >
      <div class=" w-full flex justify-between items-center ">
        <h1 class="text-xl w-full ">
          {{ tasks.taskName }}
        </h1>
        <form
          @submit.prevent="updateTask(tasks.id)"
          class="flex justify-between items-center w-full"
        >
          <input
            type="text"
            required
            class=" text-gray-500 focus:outline-none border-2"
            id="{id}"
            v-model="taskNameUpdate"
          />
          <button
            type="submit"
            class=" py-2 px-8 mr-2 rounded-sm self-start text-sm
      text-white bg-black duration-200 border-solid
      border-2 border-transparent hover:border-black  hover:bg-white
      hover:text-black uppercase font-semibold"
          >
            edit
          </button>
        </form>

        <button
          @click="deleteTask(tasks.id)"
          class=" py-2 px-6 rounded-sm self-start text-sm
      text-white bg-black duration-200 border-solid
      border-2 border-transparent hover:border-black hover:bg-white
      hover:text-black uppercase font-semibold"
        >
          delete
        </button>
      </div>
    </div>

    <!-- Status Message -->
    <div
      v-if="statusMsg || errorMsg"
      class="mb-10 p-4 bg-light-grey rounded-md shadow-lg"
    >
      <p class="text-black">
        {{ statusMsg }}
      </p>
      <p class="text-red-500">{{ errorMsg }}</p>
    </div>

    <!-- Create -->
    <div class="p-8 flex items-start bg-light-grey rounded-md shadow-lg">
      <!-- Form -->
      <form @submit.prevent="createtasks" class="flex flex-col gap-y-5 w-full">
        <h1 class="text-2xl text-black">Add a tasks</h1>

        <!-- tasks Name -->
        <div class="flex flex-col">
          <input
            type="text"
            required
            class="p-2 text-gray-500 focus:outline-none"
            id="tasks-name"
            v-model="taskName"
          />
        </div>

        <button
          type="submit"
          class="mt-6 py-2 px-6 rounded-sm self-start text-sm
      text-white bg-black duration-200 border-solid
      border-2 border-transparent hover:border-black hover:bg-white
      hover:text-black uppercase font-semibold"
        >
          Add
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref, computed } from "vue";
import { supabase } from "../supabase/init";
import store from "../store/index";

export default {
  name: "Home",
  components: {},
  setup() {
    const taskName = ref("");
    const taskNameUpdate = ref("");
    const statusMsg = ref(null);
    const errorMsg = ref(null);
    const data = ref([]);
    const dataLoaded = ref(null);
    const user = computed(() => store.state.user);

    // Create tasks
    const createtasks = async () => {
      try {
        const { error } = await supabase.from("tasks").insert([
          {
            taskName: taskName.value,
          },
        ]);
        if (error) throw error;
        statusMsg.value = "Succes: tasks Created!";
        taskName.value = null;
        getData();
        setTimeout(() => {
          statusMsg.value = false;
        }, 5000);
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    // Create tasks after update
    const createtasksUpdate = async () => {
      try {
        const { error } = await supabase.from("tasks").insert([
          {
            taskName: taskNameUpdate.value,
          },
        ]);
        if (error) throw error;
        statusMsg.value = "Succes: tasks Updated!";
        taskNameUpdate.value = null;
        getData();
        setTimeout(() => {
          statusMsg.value = false;
        }, 5000);
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    // Get data
    const getData = async () => {
      try {
        const { data: tasks, error } = await supabase.from("tasks").select("*");
        if (error) throw error;
        data.value = tasks;
        dataLoaded.value = true;
        console.log(store.state.user);
      } catch (error) {
        console.warn(error.message);
      }
    };
    getData();

    // Delete Task
    const deleteTask = async (id) => {
      try {
        const { error } = await supabase
          .from("tasks")
          .delete()
          .eq("id", id);
        if (error) throw error;
        getData();
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    //update task

    const updateTask = async (id) => {
      try {
        console.log(id);
        const { error } = await supabase
          .from("tasks")
          .update({ taskName: taskNameUpdate.value })
          .eq("id", id);
        deleteTask(id);
        createtasksUpdate();
        if (error) throw error;
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    return {
      taskName,
      statusMsg,
      errorMsg,
      createtasks,
      dataLoaded,
      data,
      deleteTask,
      user,
      updateTask,
      taskNameUpdate,
    };
  },
};
</script>
