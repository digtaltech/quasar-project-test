<template>
  <q-dialog v-model="dialogVisible" persistent>
    <q-card>
      <q-card-section>
        <q-item-label class="text-h6">{{ editedDish.dish_name }}</q-item-label>
      </q-card-section>
      <q-card-section>
        <q-input v-model="editedDish.dish_description" label="Описание блюда" />
        <q-input
          v-model="editedDish.dish_cost"
          label="Цена блюда"
          type="number"
        />
      </q-card-section>
      <q-card-actions align="right">
        <q-btn label="Сохранить" color="primary" @click="saveChanges" />
        <q-btn label="Отмена" color="negative" @click="closeDialog" />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
export default {
  props: {
    dish: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      socket: null,
      dialogVisible: false,
      editedDish: {
        dish_name: this.dish.dish_name,
        dish_description: this.dish.dish_description,
        dish_cost: this.dish.dish_cost,
      },
    };
  },
  methods: {
    openDialog() {
      this.dialogVisible = true;
      this.socket = new WebSocket("ws://178.250.156.182:8080/fortest");
      this.socket.addEventListener("message", (event) => {
        const data = JSON.parse(event.data);
        console.log("Server response:", data);
      });

      this.socket.addEventListener("error", (error) => {
        console.error("WebSocket Error:", error);
      });

      this.socket.addEventListener("close", (event) => {
        console.log("WebSocket closed:", event);
      });
    },
    closeDialog() {
      this.dialogVisible = false;
      //   if (this.socket) this.socket.close();
    },
    saveChanges() {
      console.log(this.dish.dish_id);
      if (!this.socket) return;

      // Отправляем данные описание блюда
      this.socket.send(
        JSON.stringify({
          key: "QMw3fsxIXkvPHH7YHWQa",
          operation: "change_dish_desc",
          id: this.dish.dish_id,
          desc_value: this.editedDish.dish_description,
        })
      );

      console.log(this.editedDish.dish_description);

      // Задержка перед отправкой следующего сообщения
      setTimeout(() => {
        // Отправляем данные о стоимости блюда
        this.socket.send(
          JSON.stringify({
            key: "QMw3fsxIXkvPHH7YHWQa",
            operation: "change_dish_cost",
            id: this.dish.dish_id,
            cost_value: parseFloat(this.editedDish.dish_cost),
          })
        );

        this.$emit("update-dish", this.editedDish);
        this.closeDialog();
      }, 300);
    },
  },
  beforeUnmount() {
    if (this.socket) this.socket.close();
  },
};
</script>
