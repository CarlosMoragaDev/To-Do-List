<script lang="ts" setup></script>

<template>
    <div class="mx-auto max-w-full lg:p-20 sm:p-4">
        <UCard
                class="w-full"
                :ui="{
      base: '',
      ring: '',
      divide: 'divide-y divide-gray-200 dark:divide-gray-700',
      header: { padding: 'px-4 py-5' },
      body: { padding: 'p-4', base: 'divide-y divide-gray-200 dark:divide-gray-700' },
      footer: { padding: 'p-4' }
    }"
        >
            <div class="flex justify-between">
                <h2 class="font-semibold text-xl text-gray-900 dark:text-white leading-tight">
                    Ir a Lista de Tareas
                </h2>
                <UButton size="sm" color="primary"
                         variant="solid" label="Nueva Tarea" :trailing="false"
                         to="/assignments"/>
            </div>
        </UCard>
    </div>
</template>

<style scoped></style>
