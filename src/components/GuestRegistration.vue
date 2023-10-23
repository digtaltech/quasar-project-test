<template>
  <div>
    <q-input
      ref="surnameInput"
      v-model="surname"
      label="Фамилия"
      placeholder="Иванов"
      :rules="[(val) => !!val || 'Поле Фамилия обязательно для заполнения']"
      required
    />
    <q-input
      ref="nameInput"
      v-model="name"
      label="Имя"
      placeholder="Иван"
      :rules="[(val) => !!val || 'Поле Имя обязательно для заполнения']"
      required
    />
    <q-input
      ref="patronymicInput"
      v-model="patronymic"
      label="Отчество"
      placeholder="Иванович"
      :rules="[(val) => !!val || 'Поле Отчество обязательно для заполнения']"
      required
    />
    <q-input
      ref="birthdateInput"
      v-model="birthdate"
      label="Дата рождения"
      placeholder="1990-01-01"
      mask="####-##-##"
      :rules="[
        (val) =>
          (val && /^\d{4}-\d{2}-\d{2}$/.test(val)) ||
          'Дата рождения в неверном формате',
      ]"
      required
    />
    <q-select
      ref="genderSelect"
      v-model="gender"
      :options="genderOptions"
      label="Пол"
      placeholder="Выберите пол"
      :rules="[(val) => !!val || 'Поле Пол обязательно для выбора']"
      required
    />
    <q-input
      ref="addressInput"
      v-model="address"
      label="Адрес"
      placeholder="ул. Центральная, д.1"
      :rules="[(val) => !!val || 'Поле Адрес обязательно для заполнения']"
      required
    />
    <q-input
      ref="phoneInput"
      v-model="phone"
      label="Телефон"
      placeholder="+7 (900) 123-45-67"
      mask="+7 (###) ###-##-##"
      :rules="[
        (val) => (val && val.length === 18) || 'Телефон в неверном формате',
      ]"
      required
    />
    <q-input
      ref="emailInput"
      v-model="email"
      label="Электронная почта"
      placeholder="example@email.com"
      type="email"
      :rules="[
        (val) =>
          (val && /.+@.+\..+/.test(val)) || 'Неверный формат электронной почты',
      ]"
      required
    />
    <q-btn label="Сохранить" @click="saveGuest" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      surname: "",
      name: "",
      patronymic: "",
      birthdate: "",
      gender: "",
      genderOptions: ["Мужской", "Женский"],
      address: "",
      phone: "",
      email: "",
    };
  },
  methods: {
    saveGuest() {
      const isValid = [
        this.$refs.surnameInput.validate(),
        this.$refs.nameInput.validate(),
        this.$refs.patronymicInput.validate(),
        this.$refs.birthdateInput.validate(),
        this.$refs.genderSelect.validate(),
        this.$refs.addressInput.validate(),
        this.$refs.phoneInput.validate(),
        this.$refs.emailInput.validate(),
      ].every((result) => result);

      if (isValid) {
        const socket = new WebSocket("ws://178.250.156.182:8080/fortest");

        socket.addEventListener("open", () => {
          socket.send(
            JSON.stringify({
              key: "QMw3fsxIXkvPHH7YHWQa",
              operation: "guest_regestration",
              surname: this.surname,
              forename: this.name,
              middlename: this.patronymic,
              phone: this.phone,
              email: this.email,
              birthday: this.birthdate,
              address: this.address,
            })
          );
        });

        socket.addEventListener("message", (event) => {
          console.log("Received data:", event.data);

          const responseData = JSON.parse(event.data);

          if (
            responseData.operation === "guest_regestration" &&
            responseData.status === true
          ) {
            // Очистка всех полей формы
            // this.surname = "";
            // this.name = "";
            // this.patronymic = "";
            // this.birthdate = "";
            // this.gender = "";
            // this.address = "";
            // this.phone = "";
            // this.email = "";

            // Переключение на вкладку "Таблица гостей"
            // this.$parent.tab = "table";
          }
          // Обработка ответа от сервера, если это необходимо
        });

        socket.addEventListener("error", (error) => {
          console.error("WebSocket Error:", error);
        });

        socket.addEventListener("close", () => {
          console.log("WebSocket connection closed.");
        });
      } else {
        console.log("Ошибка валидации");
      }
    },
  },
};
</script>
