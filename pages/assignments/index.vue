<script lang="ts" setup>

const isLoading = ref(true);
const dataInitial = ref('')
const assingmentsData = ref([])

const {data: assignments, refresh} =    await useAsyncData(
        'assignments', () => $fetch('http://localhost:8000/api/assignments')
            .then(response => {
                assingmentsData.value = [];
                dataInitial.value = response
                response.forEach((element) => {
                    assingmentsData.value.push(element)
                });
            })
    )


setTimeout(() => {
    refresh()
    isLoading.value = false;
}, 100);

const itemsTable = assingmentsData; // assignments es tu variable que contiene los datos
const pageSize = 10;
const currentPage = ref(0);

const totalPages = computed(() => Math.ceil(itemsTable?.value?.length / pageSize));

let displayedItems = computed(() => {
    const start = currentPage.value * pageSize;
    const end = start + pageSize;
    return itemsTable?.value?.slice(start, end);
});
const previousPage = () => {
    if (currentPage.value > 0) {
        currentPage.value--;
    }
};

const nextPage = () => {
    if (currentPage.value < totalPages.value - 1) {
        currentPage.value++;
    }
};

const deleteFunction = async (id) => {
    try {
        await useFetch('http://localhost:8000/api/assignments/' + id, {
            method: "DELETE",
        }).then((response) => {
            console.log(response)
            isLoading.value = true;
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
    <div class="max-w-full mx-auto bg-white shadow-md rounded my-6 lg:p-20 sm:p-4 px-4 md:px-8">
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
                <div class="items-start justify-between md:flex shadow-xl p-4">
                    <div class="max-w-lg">
                        <h3 class="text-gray-800 text-xl font-bold sm:text-2xl leading-tight">
                            Lista de Tareas
                        </h3>
                    </div>
                    <div class="mt-3 md:mt-0">
                        <UButton size="sm" color="primary"
                                 variant="solid" label="Nueva Tarea" :trailing="false"
                                 to="assignments/create"/>
                    </div>
                </div>
            </template>
            <div v-if="isLoading" class="grid justify-items-center">
                <UButton loading>Cargando Información</UButton>
            </div>
            <div v-else>
                <div class="shadow-xl border rounded-lg overflow-x-auto">
                    <table class="w-full table-auto text-sm text-left shadow-2xl">
                        <thead class="bg-gray-50 text-gray-600 font-medium border-b">
                        <tr class="bg-gray-200">
                            <th class="py-3 px-6">Nombre</th>
                            <th class="py-3 px-6 hidden sm:table-cell">Descripción</th>
                            <th class="py-3 px-6">Estado</th>
                            <th class="py-3 px-6">Acciones</th>
                        </tr>
                        </thead>
                        <tbody class="text-gray-600 divide-y">
                        <tr v-for="(item, index) in displayedItems" :key="index">
                            <td class="border px-6 py-4 whitespace-nowrap">{{ item?.name }}</td>
                            <td class="border px-6 py-4 hidden sm:table-cell">{{ item?.description }}</td>
                            <td class="border px-6 py-4 whitespace-nowrap">
                                <UBadge class="whitespace-nowrap px-3 py-2"
                                        :color="item?.status === 'Iniciada' ? 'primary' :
                                item?.status === 'Pendiente' ? 'yellow' :  'cyan'">{{ item?.status }}
                                </UBadge>
                            </td>
                            <td class="border px-6 py-4">
                                <div class="flex space-x-2">
                                    <NuxtLink :to="'/assignments/' + item?.id"
                                              class="bg-blue-500 text-white rounded hover:bg-blue-600 py-2 px-3 font-medium duration-150">
                                        Editar
                                    </NuxtLink>
                                    <UPopover mode="click">
                                        <UButton
                                                class="py-2 leading-none px-3 font-medium bg-red-500 text-white rounded hover:bg-red-600"
                                                label="Eliminar"/>
                                        <template #panel="{ close }">
                                            <div class="p-4">
                                                ¿Está seguro?
                                            </div>
                                            <div class="flex space-x-2 p-3">
                                                <UButton label=Si @click="deleteFunction(item?.id);close()"
                                                         class="py-2 leading-none px-3 font-medium bg-red-500 text-white rounded hover:bg-red-600"></UButton>
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
            </div>
        </UCard>
        <div class="flex justify-end mt-4">
            <button @click="previousPage" :disabled="currentPage === 0" class="px-3 py-1 mr-2 bg-gray-300 text-gray-600 rounded hover:bg-gray-400">Anterior</button>
            <button @click="nextPage" :disabled="currentPage === totalPages - 1" class="px-3 py-1 bg-gray-300 text-gray-600 rounded hover:bg-gray-400">Siguiente</button>
        </div>
    </div>
</template>

<style scoped></style>
