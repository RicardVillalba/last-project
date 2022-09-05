<template>
  <div class="max-w-screen-md mx-auto px-4 py-10">
    <div
      class="flex flex-col items-center p-8 shadow-md cursor-pointer"
      v-for="(tasks, index) in data"
      :key="index"
    >
      <div class=" flex ">
        <h1 class="text-xl ">
          {{ tasks.taskName }}
        </h1>
        <div class=" w-[50%] flex flex-row justify-between content-between">
          <button >
            <img class="bg-black" src="../assets/images/edit.png" alt="edit" />
          </button>
          <button>
            <img
              class="bg-black "
              @click="deleteTask(tasks.id)" 
              src="../assets/images/delete.png"
              alt="delete"
            />
          </button>
          <h1>{{ tasks.id }}</h1>
        </div>
      </div>
    </div>

    <!-- Status Message -->
    <div
      v-if="statusMsg || errorMsg"
      class="mb-10 p-4 bg-light-grey rounded-md shadow-lg"
    >
      <p class="text-at-light-green">
        {{ statusMsg }}
      </p>
      <p class="text-red-500">{{ errorMsg }}</p>
    </div>

    <!-- Create -->
    <div class="p-8 flex items-start bg-light-grey rounded-md shadow-lg">
      <!-- Form -->
      <form @submit.prevent="createtasks" class="flex flex-col gap-y-5 w-full">
        <h1 class="text-2xl text-at-light-green">Add a tasks</h1>

        <!-- tasks Name -->
        <div class="flex flex-col">
          <label for="tasks-name" class="mb-1 text-sm text-at-light-green"
            >tasks Name</label
          >
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
      text-white bg-at-light-green duration-200 border-solid
      border-2 border-transparent hover:border-at-light-green hover:bg-white
      hover:text-at-light-green"
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
        console.log(data.value);
      } catch (error) {
        console.warn(error.message);
      }
    };
    getData();

    // Delete Task

    
    const deleteTask = async (id) => {
      try {
        console.log(id);
        const { error } = await supabase
          .from("tasks")
          .delete()
          .eq("id", id)
        if (error) throw error;
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    // const deleteTask = (id) => {
    //   if (data.value) {
    //     data.value = data.value.filter(
    //       (task) => task.id !== id
    //     );
    //     return;
    //   }
    //   errorMsg.value = "Error: Cannot remove";
    //   setTimeout(() => {
    //     errorMsg.value = false;
    //   }, 5000);
    // };







   

    return {
      taskName,
      statusMsg,
      errorMsg,
      createtasks,
      dataLoaded,
      data,
      deleteTask,
      user,
    };
  },
};
</script>
