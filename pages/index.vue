<script lang="ts" setup>
import Default from "~/layouts/Default.vue";

definePageMeta({
    layout: 'Default'
})
</script>

<template>
    <Default>
        <main class="w-full h-screen flex flex-col items-center justify-center px-4">
            <UCard :ui="{divide: 'divide-y divide-gray-200 dark:divide-gray-700', body: { padding: 'p-4', base: 'divide-y divide-gray-200 dark:divide-gray-700' },}"
            >
                <div class="max-w-sm w-full text-gray-600 space-y-8">
                    <div class="text-center">
                        <div class="mt-5 space-y-2">
                            <h3 class="text-gray-800 text-2xl font-bold sm:text-3xl">
                                Bienvenido
                            </h3>
                            <p class="">
                                Aqui podras registrar y administrar tus tareas
                            </p>
                        </div>
                    </div>
                    <div class="grid justify-items-center">
                        <UButton
                                class="mt-4 px-4 py-2 text-white font-medium bg-green-400 hover:bg-green-600 active:bg-indigo-600 rounded-lg duration-150"
                                to="/assignments">
                            Ir al panel
                        </UButton>
                    </div>
                </div>
            </UCard>
        </main>
    </Default>
</template>

<style scoped></style>
