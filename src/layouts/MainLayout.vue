<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          {{ pageTitle }}
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header> Navigation </q-item-label>

        <q-item
          clickable
          v-for="link in navigationLinks"
          :key="link.title"
          @click="navigateTo(link.route)"
        >
          <q-item-section>
            {{ link.title }}
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, computed } from "vue";
import { useRoute, useRouter } from "vue-router";

export default defineComponent({
  name: "MainLayout",

  setup() {
    const leftDrawerOpen = ref(false);
    const route = useRoute();
    const router = useRouter();

    const navigationLinks = [
      { title: "Меню блюд", route: "/" },
      { title: "Гости", route: "/guests" },
    ];

    const pageTitle = computed(() => {
      if (route.path === "/") {
        return "Меню блюд";
      }
      return "Quasar App";
    });

    function navigateTo(targetRoute) {
      if (targetRoute !== route.path) {
        router.push(targetRoute); // выполните переход на другую страницу
        leftDrawerOpen.value = false;
      }
    }

    return {
      navigationLinks,
      pageTitle,
      leftDrawerOpen,
      navigateTo,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>
