
<template>
  <div class="max-w-screen-md mx-auto px-4 py-10">
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
  import { ref } from "vue";
// import { uid } from "uid";
import { supabase } from "../supabase/init";


export default {
  name: "Home",
  components: {},
  setup() {

    const taskName = ref("");
    const statusMsg = ref(null);
    const errorMsg = ref(null);
   
 
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



    // Create data / vars
    const data = ref([]);
const dataLoaded = ref(null);


    // Get data
const getData = async()=> {
  try {
    const { data: tasks, error } = await supabase.from("tasks").select('*');
    if(error) throw error;
    data.value = tasks;
    dataLoaded.value = true;
    console.log(data.value);
  } catch (error) {
    console.warn(error.message);
  }
}
    // Run data function
getData();



    return {
      taskName,
      statusMsg,
      errorMsg,
      createtasks,
      getData,
      dataLoaded,
    };


    
  },
};
</script>
