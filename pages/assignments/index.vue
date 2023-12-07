<script lang="ts" setup>
const isLoading = ref(true);
const {data: assignments, refresh} = await useAsyncData(
    'assignments', () => $fetch('http://localhost:8000/api/assignments'))

setTimeout(() => {
    refresh()
    isLoading.value = false;
}, 100);


const deleteFunction = async (id) => {
    try {
        await useFetch('http://localhost:8000/api/assignments/' + id, {
            method: "DELETE",
        }).then((response) => {
            console.log(response)
            setTimeout(() => {
                refresh()
                isLoading.value = false;
            }, 100);
        })
    } catch (e) {
        console.log(e)
    }
}

</script>

<template>
    <div class="max-w-full mx-auto bg-white shadow-md rounded my-6 lg:p-20 sm:p-4">
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
            <template #header>
                <div class="flex justify-between shadow-xl p-4">
                    <h2 class="font-semibold text-xl text-gray-900 dark:text-white leading-tight">
                        Lista de Tareas
                    </h2>
                    <UButton size="sm" color="primary"
                             variant="solid" label="Nueva Tarea" :trailing="false"
                             to="assignments/create"/>
                </div>
            </template>
            <div v-if="isLoading" class="grid justify-items-center">
                <UButton loading>Cargando Información</UButton>
            </div>
            <div v-else>
                <table class="w-full table-auto shadow-2xl">
                    <thead>
                    <tr class="bg-gray-200">
                        <th class="px-4 py-2">Nombre</th>
                        <th class="px-4 py-2 hidden sm:table-cell">Descripción</th>
                        <th class="px-4 py-2">Estado</th>
                        <th class="px-4 py-2">Acciones</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-for="(item, index) in assignments" :key="index">
                        <td class="border px-4 py-2">{{ item?.name }}</td>
                        <td class="border px-4 py-2 hidden sm:table-cell">{{ item?.description }}</td>
                        <td class="border px-4 py-2">
                            <UBadge class="whitespace-nowrap"
                                    :color="item?.status === 'Iniciada' ? 'primary' :
                                item?.status === 'Pendiente' ? 'yellow' :  'cyan'">{{ item?.status }}
                            </UBadge>
                        </td>
                        <td class="border px-4 py-2">
                            <div class="flex space-x-2">
                                <NuxtLink :to="'/assignments/' + item?.id"
                                          class="px-3 py-1 bg-blue-500 text-white rounded hover:bg-blue-600">Editar
                                </NuxtLink>
                                <UPopover mode="click">
                                    <UButton class="px-3 py-1 bg-red-500 text-white rounded hover:bg-red-600"
                                             label="Eliminar"/>
                                    <template #panel="{ close }">
                                        <div class="p-4">
                                            ¿Está seguro?
                                        </div>
                                        <div class="flex space-x-2">
                                            <UButton label=Si @click="deleteFunction(item?.id);close()"
                                                     class="px-3 py-1 bg-green-500 text-white rounded hover:bg-green-600"></UButton>
                                            <UButton label="No" @click="close"/>
                                        </div>
                                    </template>
                                </UPopover>
                            </div>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </div>
        </UCard>
    </div>
</template>

<style scoped></style>
