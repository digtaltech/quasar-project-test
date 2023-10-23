<template>
    <q-page class="flex flex-center">
      <div>
        <div class="text-h4 q-my-md">Меню блюд</div>
  
        <div class="q-pa-md row items-start q-gutter-md">
          <dish-card
            v-for="dish in dishes"
            :key="dish.dish_id"
            :dish="dish" 
          />
        </div>
      </div>
    </q-page>
  </template>
  
  <script>
  import { ref, onBeforeUnmount } from "vue";
  import DishCard from "components/DishCard.vue";
  
  export default {
    components: {
      DishCard,
    },
    setup() {
      const dishes = ref([]);
      const socket = new WebSocket("ws://178.250.156.182:8080/fortest");
  
      socket.addEventListener("open", () => {
        socket.send(
          JSON.stringify({
            key: "QMw3fsxIXkvPHH7YHWQa",
            operation: "dishes_list",
          })
        );
      });
  
      socket.addEventListener("message", (event) => {
        const data = JSON.parse(event.data);
        if (data.operation === "dishes_list") {
          dishes.value = data.dishes_list;
        }
      });
  
      onBeforeUnmount(() => {
        socket.close();
      });
  
      return {
        dishes,
      };
    },
  };
  </script>
  
  <style scoped>
  /* Здесь вы можете добавить любые стили, если они нужны */
  </style>
  