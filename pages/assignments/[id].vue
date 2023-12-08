<script lang="ts" setup>
import {ref, reactive} from "vue";

const route = useRoute()
const router = useRouter()
const assignment = ref(route.params.id)

const {data: assignments, refresh} = await useAsyncData(
    'assignments', () => $fetch('http://localhost:8000/api/assignments/' + assignment.value))

const isLoading = ref(true);
setTimeout(() => {
    refresh()
    isLoading.value = false;
}, 100);

const isOpen = ref(false)

const state = reactive({
    name: assignments?.value?.name,
    description: assignments?.value?.description,
})


const updateFunction = async () => {
    await useFetch('http://localhost:8000/api/assignments/' + assignment.value, {
        method: "PUT",
        params: {
            id: assignment.value,
            name: state.name,
            description: state.description,
            status: '1',
        },
    }).then((response) => {
        console.log(response)
        refresh()
        isOpen.value = !isOpen.value
    })
}
</script>

<template>
    <div class="bg-gray-100 min-h-screen py-12">
        <UCard :ui="{ ring: 'ring-1 shadow-xl ring-gray-100/5 dark:ring-gray-800',}">
            <template #header>
                <div
                        class="grid grid-cols-2 rounded-lg p-5 shadow-xl dark:shadow-xl dark:shadow-cyan-500/5 ring-1 ring-gray-100/5 dark:ring-gray-800">
                    <div class="col-start-1 col-span-1">
                        Crear Nueva Tarea
                    </div>
                    <div class="col-start-2 col-span-1 grid justify-items-end">
                        <div class="flex items-center justify-between space-x-2 pr-12">
                            <UButton type="submit" size="sm" color="primary"
                                     variant="solid" label="Editar"
                                     @click="isOpen = true"/>
                                <UButton size="sm" color="primary"
                                         variant="solid" label="Volver" :trailing="false"
                                         to="/assignments"/>
                        </div>
                    </div>
                </div>
            </template>
            <div class="max-w-7xl mx-auto bg-white rounded-lg shadow-xl p-8">
                <h2 class="text-2xl font-semibold mb-4">Detalles {{ assignments?.name }}</h2>
                <div>
                    <div class="mb-4">
                        <p class="text-gray-600 mb-1"><span class="font-semibold">Id:</span></p>
                        <p class="border-b border-gray-300 pb-2">{{ assignments?.id }}</p>
                    </div>
                    <div class="mb-4">
                        <p class="text-gray-600 mb-1"><span class="font-semibold">Nombre:</span></p>
                        <p class="border-b border-gray-300 pb-2">{{ assignments?.name }}</p>
                    </div>
                    <div class="mb-4">
                        <p class="text-gray-600 mb-1"><span class="font-semibold">Descripción:</span></p>
                        <p class="border-b border-gray-300 pb-2">{{ assignments?.description }}</p>
                    </div>
                    <div class="mb-4">
                        <p class="text-gray-600 mb-1"><span class="font-semibold">Estado:</span></p>
                        <p class="border-b border-gray-300 pb-2">
                            <UBadge class="whitespace-nowrap"
                                    :color="assignments?.status === 'Iniciada' ? 'primary' :
                                assignments?.status === 'Pendiente' ? 'yellow' :  'cyan'">{{ assignments?.status }}
                            </UBadge>
                        </p>
                    </div>
                </div>
            </div>
        </UCard>
    </div>
    <USlideover v-model="isOpen" :ui="{width: 'lg:max-w-4xl md:max-w-lg sm:max-w-lg'}">
        <UForm
                :state="state"
                @submit="updateFunction">
            <UCard class="flex flex-col flex-1"
                   :ui="{ body: { base: 'flex-1' }, ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
                <template #header>
                    <div
                            class="grid grid-cols-2 rounded-lg p-5 shadow-xl dark:shadow-xl dark:shadow-cyan-500/5 ring-1 ring-gray-100/5 dark:ring-gray-800">
                        <div class="col-start-1 col-span-1">
                            Editar Tarea
                        </div>
                        <div class="col-start-2 col-span-1 grid justify-items-end">
                            <div class="flex items-center justify-between space-x-2 pr-12">
                                <UButton type="submit" size="sm" color="primary"
                                         variant="solid" label="Guardar"/>
                                <UButton size="sm" color="primary"
                                         variant="solid" label="Cerrar" :trailing="false"
                                         @click="isOpen = false"/>
                            </div>
                        </div>
                    </div>
                </template>
                <div
                        class="grid lg:grid-cols-4 sm:grid-cols-1 shadow-xl dark:shadow-xl dark:shadow-cyan-500/5 rounded-lg p-2 ring-3 ring-gray-100/5 dark:ring-gray-800">
                    <div class="lg:col-start-2 sm:col-start-1 lg:col-span-2 pr-5 pb-5">
                        <UFormGroup label="Nombre" name="name" class="m-5" :required="true">
                            <UInput v-model="state.name"/>
                        </UFormGroup>
                        <UFormGroup label="Descripción" name="description" class="m-5" help="Máximo 100 caracteres">
                            <UTextarea v-model="state.description" size="lg" :rows="3" :maxlength="100"/>
                        </UFormGroup>
                    </div>
                </div>
            </UCard>
        </UForm>
    </USlideover>
</template>

<style scoped></style>
