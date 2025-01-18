<script setup lang="ts">
import { computed, onMounted, reactive } from "vue";
import { routes } from "@/router";
import AppPage from "@/components/AppPage.vue";
import AppLink from "@/components/AppLink.vue";
import { initData, useSignal, type User } from "@telegram-apps/sdk-vue";
import { ofetch } from "ofetch";

const nonIndexRoutes = computed(() => routes.filter((r) => !!r.meta?.title));
const initDataRaw = useSignal(initData.raw);
const initDataRef = useSignal(initData.state);

const validateTelegramUser = async () => {
  try {
    const response = await ofetch(
      `${import.meta.env.VITE_BACKEND}/validateTelegramUser`,
      {
        method: "POST",
        body: {
          initDataRaw: initDataRaw.value,
        },
      }
    );

    alert(JSON.stringify(response));
    pageState.isValidTelegramUser = false;
  } catch (error) {
    console.log(error);
  }
};

const pageState = reactive({
  isValidTelegramUser: false,
});

onMounted(async () => {
  pageState.isValidTelegramUser = false;
  await validateTelegramUser();
});

console.log("import.meta.env.VITE_BACKEND: ", import.meta.env.VITE_BACKEND);
</script>

<template>
  <AppPage title="" :back="false">
    <h1>Hello {{ initDataRef?.user?.firstName }}</h1>

    <button @click="validateTelegramUser">Validate</button>
    <ul class="index-page__links">
      <li
        v-for="route in nonIndexRoutes"
        :key="route.name"
        class="index-page__link-item"
      >
        <AppLink class="index-page__link" :to="{ name: route.name }">
          <i v-if="route.meta?.icon" class="index-page__link-icon">
            <component :is="route.meta.icon" />
          </i>
          {{ route.meta!.title }}
        </AppLink>
      </li>
    </ul>
  </AppPage>
</template>

<style scoped>
.index-page__links {
  list-style: none;
  padding-left: 0;
}

.index-page__link {
  font-weight: bold;
  display: inline-flex;
  gap: 5px;
}

.index-page__link-item + .index-page__link-item {
  margin-top: 10px;
}

.index-page__link-icon {
  width: 20px;
  display: block;
}

.index-page__link-icon svg {
  display: block;
}
</style>
