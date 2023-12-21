<script lang="ts" setup>
import Default from "~/layouts/Default.vue";

const isLoading = ref(true);
const dataInitial = ref('')
const assingmentsData = ref([])

const {data: assignments, refresh} = await useAsyncData(
    'assignments', () => $fetch('http://localhost:8000/api/assignments')
        .then(response => {
            assingmentsData.value = [];
            dataInitial.value = response
            response.forEach((element) => {
                if (element.status === 'Completada') {
                    assingmentsData.value.push(element)
                }
            });
        })
)
console.log(assingmentsData.value)

setTimeout(() => {
    refresh()
    isLoading.value = false;
}, 100);

const itemsTable = assingmentsData;
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
    <Default>
        <Navbar />
        <div class="min-w-full min-h-full lg:p-4 sm:p-1">
            <UCard
                    class="min-w-full min-h-full bg-gradient-to-b from-rose-100 to-teal-100"
                    :ui="{
                    divide: 'divide-y divide-gray-200 dark:divide-gray-700',
                    body: { padding: 'p-4', base: 'divide-y divide-gray-200 dark:divide-gray-700' }}" >
                <template #header>
                    <div class="items-start justify-between md:flex shadow-xl p-4">
                        <div class="max-w-lg">
                            <h3 class="text-gray-800 text-xl font-bold sm:text-2xl leading-tight">
                                Lista de Tareas Archivadas
                            </h3>
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
                                <th class="py-3 px-6 hidden sm:table-cell">Estado</th>
                                <th class="py-3 px-6">Acciones</th>
                            </tr>
                            </thead>
                            <tbody class="text-gray-600 divide-y">
                            <tr v-for="(item, index) in displayedItems" :key="index">
                                <td class="border px-6 py-4 ">{{ item?.name }}</td>
                                <td class="border px-6 py-4 hidden sm:table-cell">{{ item?.description }}</td>
                                <td class="border px-6 py-4 hidden sm:table-cell ">
                                    <UBadge class=" px-3 py-2"
                                            :color="item?.status === 'Iniciada' ? 'primary' :
                                item?.status === 'Pendiente' ? 'yellow' :  'cyan'">{{ item?.status }}
                                    </UBadge>
                                </td>
                                <td class="border px-6 py-4">
                                    <div class="flex space-x-2">
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
                <div class="flex justify-end pt-2">
                    <UButton @click="previousPage" :disabled="currentPage === 0"
                             class="px-3 py-1 mr-2 bg-green-400 text-white rounded hover:bg-green-600">Anterior
                    </UButton>
                    <div class="mr-3"><span>Página: </span>{{ currentPage + 1 }}</div>
                    <UButton @click="nextPage" :disabled="currentPage === totalPages - 1"
                             class="px-3 py-1 mr-2 bg-green-400 text-white rounded hover:bg-green-600">Siguiente
                    </UButton>
                </div>
            </UCard>
        </div>
    </Default>
</template>

<style scoped></style>
