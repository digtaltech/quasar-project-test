<template>
  <q-card class="my-card" flat bordered>
    <q-card-section>
      <q-card-section>
        <div class="row items-center no-wrap">
          <div class="col">
            <q-item-label class="">{{ dish.dish_name }}</q-item-label>
          </div>

          <div class="col-auto">
            <q-btn color="grey-7" round flat icon="more_vert">
              <q-menu cover auto-close>
                <q-list>
                  <q-item clickable @click="openEditPopup">
                    <q-item-section>Редактировать</q-item-section>
                  </q-item>
                </q-list>
              </q-menu>
            </q-btn>
          </div>
        </div>
      </q-card-section>

      <q-img
        class="img-style"
        :src="getImageUrl(dish.dish_image)"
        alt="Dish Image"
      />

      <q-card-section class="q-pt-none">
        <div class="text-caption text-grey overflow-text">
          {{ dish.dish_description }}
        </div>
        <div class="text-subtitle1">Цена: {{ dish.dish_cost }}</div>
      </q-card-section>
    </q-card-section>
    <edit-dish-popup
      :dish="dish"
      ref="editDishPopup"
      @update-dish="updateDish"
    />
  </q-card>
</template>

<script>
import EditDishPopup from "../components/EditDishPopup.vue";

export default {
  props: {
    dish: {
      type: Object,
      required: true,
    },
  },
  methods: {
    getImageUrl(base64String) {
      return `data:image/jpeg;base64,${base64String}`;
    },
    openEditPopup() {
      this.$refs.editDishPopup.openDialog();
    },
    updateDish(updatedDish) {
      Object.assign(this.dish, updatedDish);
    },
  },
  components: {
    EditDishPopup,
  },
};
</script>

<style lang="sass" scoped>
/* Стили компонента */
.my-card
    width: 100%
    max-width: 250px
    height: 370px

.overflow-text
    white-space: nowrap
    overflow: hidden
    text-overflow: ellipsis

.img-style
    height: 200px
</style>
