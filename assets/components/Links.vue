<template>
  <div class="flex items-center justify-end gap-4">
    <slot name="more-items"></slot>
    <Dropdown class="dropdown-end" @closed="latestTag = latest?.tag ?? config.version">
      <template #trigger>
        <mdi:announcement class="size-6 -rotate-12" />
        <span
          class="bg-red absolute top-0 right-px size-2 rounded-full"
          v-if="hasUpdate && latestTag != latest?.tag"
        ></span>
      </template>
      <template #content>
        <div class="w-72">
          <Releases />
        </div>
      </template>
    </Dropdown>

    <router-link
      :to="{ name: '/settings' }"
      :aria-label="$t('title.settings')"
      data-testid="settings"
      class="btn btn-circle btn-sm"
    >
      <mdi:cog class="size-6" />
    </router-link>

    <dropdown class="dropdown-end" v-if="config.user">
      <template #trigger>
        <img
          class="ring-base-content/60 size-6 max-w-none rounded-full p-px ring-1"
          :src="withBase('/api/profile/avatar')"
        />
      </template>
      <template #content>
        <div class="p-2">
          <div class="font-bold">
            {{ config.user.name }}
          </div>
          <div class="text-sm font-light">
            {{ config.user.email }}
          </div>
        </div>
        <ul class="menu mt-4 p-0">
          <li v-if="config.authProvider === 'simple'">
            <button @click.prevent="logout()" class="text-primary p-2">{{ $t("button.logout") }}</button>
          </li>
        </ul>
      </template>
    </dropdown>
  </div>
</template>
<script lang="ts" setup>
async function logout() {
  await fetch(withBase("/api/token"), {
    method: "DELETE",
  });

  location.reload();
}

const { hasUpdate, latest } = useReleases();
const latestTag = useProfileStorage("releaseSeen", config.version);
</script>
