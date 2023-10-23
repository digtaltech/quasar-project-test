<template>
  <div>
    <q-table :rows="guests" :columns="columns" row-key="id">
      <template v-slot:body-cell-addPhone="props">
        <q-td :props="props">
          <q-btn
            flat
            color="primary"
            label="Добавить"
            @click="openPhoneDialog(props.row)"
          />
        </q-td>
      </template>
    </q-table>

    <!-- Модальное окно для добавления телефона -->
    <q-dialog v-model="phoneDialog" persistent>
      <q-card>
        <q-card-section>
          <q-input
            v-model="newPhone"
            label="Номер телефона"
            type="text"
            required
          />
        </q-card-section>
        <q-card-actions align="right">
          <q-btn label="Сохранить" color="primary" @click="addPhone" />
          <q-btn label="Отмена" color="negative" @click="closePhoneDialog" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      guests: [],
      columns: [
        { id: "id", label: "id", field: (row) => row.id },
        { name: "surname", label: "Фамилия", field: (row) => row.surname },
        { name: "forename", label: "Имя", field: (row) => row.forename },
        {
          name: "middlename",
          label: "Отчество",
          field: (row) => row.middlename,
        },
        {
          name: "birthday",
          label: "День рождения",
          field: (row) => row.birthday,
        },
        { name: "address", label: "Адрес", field: (row) => row.address },
        {
          name: "phones",
          label: "Телефоны",
          field: (row) => row.phones.join(", "),
        },
        { name: "addPhone", label: "Добавить телефон" },
      ],
      phoneDialog: false, // Состояние модального окна
      newPhone: "", // Новый номер телефона для добавления
      selectedGuest: null, // Выбранный гость
    };
  },

  created() {
    this.loadGuestsList(); // Загрузка списка гостей при создании компонента
  },

  methods: {
    loadGuestsList() {
      const socket = new WebSocket("ws://178.250.156.182:8080/fortest");

      socket.addEventListener("open", () => {
        socket.send(
          JSON.stringify({
            key: "QMw3fsxIXkvPHH7YHWQa",
            operation: "guests_list",
          })
        );
      });

      socket.addEventListener("message", (event) => {
        const data = JSON.parse(event.data);

        if (data.operation === "guests_list") {
          this.guests = data.guest_list;
        }
      });
    },

    openPhoneDialog(row) {
      this.phoneDialog = true;
      this.selectedGuest = row;
    },

    closePhoneDialog() {
      this.phoneDialog = false;
      this.selectedGuest = null;
      this.newPhone = "";
    },

    addPhone() {
      // Вызываем запрос через вебсокеты для добавления телефона
      if (!this.selectedGuest || !this.newPhone) {
        return;
      }

      const socket = new WebSocket("ws://178.250.156.182:8080/fortest");

      console.log("send to socket");

      socket.addEventListener("open", () => {
        socket.send(
          JSON.stringify({
            key: "QMw3fsxIXkvPHH7YHWQa",
            operation: "add_guestphone",
            id: this.selectedGuest.id,
            phone: this.newPhone,
          })
        );
      });

      socket.addEventListener("message", (event) => {
        const data = JSON.parse(event.data);
        console.log(data);
        if (data.status) {
          // Обработка успешного добавления телефона
          console.log("Телефон успешно добавлен:", data);

          // Обновляем данные в this.guests
          const updatedGuests = this.guests.map((guest) => {
            if (guest.id === this.selectedGuest.id) {
              // Находим выбранного гостя и обновляем его номера телефонов
              guest.phones.push(this.newPhone);
            }
            return guest;
          });

          this.guests = updatedGuests; // Обновляем данные в объекте this.guests
        } else {
          // Обработка ошибки при добавлении телефона
          console.error("Ошибка при добавлении телефона:");
        }
        // Закрываем модальное окно после выполнения операции
        this.closePhoneDialog();
      });
    },
  },
};
</script>
