<script lang="ts" setup>
import {ref, reactive} from "vue";
import Default from "~/layouts/Default.vue";
import type {FormError, FormSubmitEvent} from '#ui/types'

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

const validate = (state: any): FormError[] => {
    const errors = []
    if (!state.name) errors.push({path: 'name', message: 'Campo Requiredo'})
    return errors
}


const updateFunction = async (event: FormSubmitEvent<any>) => {
    await useFetch('http://localhost:8000/api/assignments/' + assignment.value, {
        method: "PUT",
        params: {
            id: assignment.value,
            name: state.name,
            description: state.description,
        },
    }).then((response) => {
        console.log(response)
        refresh()
        isOpen.value = !isOpen.value
    })
}

const pendingAssignment = async () => {
    await useFetch('http://localhost:8000/api/status_assignment/', {
        method: "PUT",
        params: {
            id: assignment.value,
            status: '2',
        },
    }).then((response) => {
        console.log(response)
        refresh()
    })
}
const finishAssignment = async () => {
    await useFetch('http://localhost:8000/api/status_assignment/', {
        method: "PUT",
        params: {
            id: assignment.value,
            status: '3',
        },
    }).then((response) => {
        console.log(response)
        refresh()
    })
}

const initatedAssignment = async () => {
    await useFetch('http://localhost:8000/api/status_assignment/', {
        method: "PUT",
        params: {
            id: assignment.value,
            status: '1',
        },
    }).then((response) => {
        console.log(response)
        refresh()
    })
}
</script>

<template>
    <Default>
        <Navbar/>
        <div class="min-w-full min-h-full lg:p-4 sm:p-1">
            <UCard
                    class="min-w-full min-h-full bg-gradient-to-b from-rose-100 to-teal-100"
                    :ui="{ ring: 'ring-1 shadow-xl ring-gray-100/5 dark:ring-gray-800',}">
                <template #header>
                    <div
                            class="grid grid-cols-2 rounded-lg p-5 shadow-xl dark:shadow-xl dark:shadow-cyan-500/5 ring-1 ring-gray-100/5 dark:ring-gray-800">
                        <div class="col-start-1 col-span-1">
                            <h2 class="text-2xl font-semibold mb-4">Detalles de Tarea: {{ assignments?.name }}</h2>
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
                <div class="mx-auto rounded-lg shadow-xl p-8">
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
                        <div v-if="assignments?.status === 'Iniciada'">
                            <div class="mb-4 flex space-x-20">
                                <div>
                                    <UPopover class="w-24">
                                        <UButton
                                                class="py-2 leading-none px-3 font-medium bg-yellow-500 text-white rounded hover:bg-yellow-600"
                                                label="Dejar Pendiente"
                                                v-show=" assignments?.status === 'Iniciada'"/>
                                        <template #panel="{ close }">
                                            <div class="p-4">
                                                ¿Está seguro?
                                            </div>
                                            <div class="flex space-x-2 p-3">
                                                <UButton label=Si @click="pendingAssignment();close()"
                                                         class="py-2 leading-none px-3 font-medium bg-red-500 text-white rounded hover:bg-red-600"></UButton>
                                                <UButton label="No" @click="close"/>
                                            </div>
                                        </template>
                                    </UPopover>
                                </div>
                                <div>
                                    <UPopover class="w-24">
                                        <UButton
                                                class="py-2 leading-none px-3 font-medium bg-cyan-500 text-white rounded hover:bg-cyan-600"
                                                label="Finalizar Tarea" v-show=" assignments?.status != 'Completada'"/>
                                        <template #panel="{ close }">
                                            <div class="p-4">
                                                ¿Está seguro?
                                            </div>
                                            <div class="flex space-x-2 p-3">
                                                <UButton label=Si @click="finishAssignment();close()"
                                                         class="py-2 leading-none px-3 font-medium bg-red-500 text-white rounded hover:bg-red-600"></UButton>
                                                <UButton label="No" @click="close"/>
                                            </div>
                                        </template>
                                    </UPopover>
                                </div>
                            </div>
                        </div>
                        <div v-else>
                            <div class="mb-4 flex space-x-4">
                                <div>
                                    <UPopover class="w-24">
                                        <UButton
                                            class="py-2 leading-none px-3 font-medium bg-green-500 text-white rounded hover:bg-green-600"
                                            label="Iniciar"
                                            v-show=" assignments?.status === 'Pendiente'"/>
                                        <template #panel="{ close }">
                                            <div class="p-4">
                                                ¿Está seguro?
                                            </div>
                                            <div class="flex space-x-2 p-3">
                                                <UButton label=Si @click="initatedAssignment();close()"
                                                         class="py-2 leading-none font-medium bg-red-500 text-white rounded hover:bg-red-600"></UButton>
                                                <UButton label="No" @click="close"/>
                                            </div>
                                        </template>
                                    </UPopover>
                                </div>
                                <div>
                                    <UPopover class="w-24">
                                        <UButton
                                            class="py-2 leading-none px-3 font-medium bg-cyan-500 text-white rounded hover:bg-cyan-600"
                                            label="Finalizar Tarea" v-show=" assignments?.status != 'Completada'"/>
                                        <template #panel="{ close }">
                                            <div class="p-4">
                                                ¿Está seguro?
                                            </div>
                                            <div class="flex space-x-2 p-3">
                                                <UButton label=Si @click="finishAssignment();close()"
                                                         class="py-2 leading-none px-3 font-medium bg-red-500 text-white rounded hover:bg-red-600"></UButton>
                                                <UButton label="No" @click="close"/>
                                            </div>
                                        </template>
                                    </UPopover>
                                </div>
                            </div>
                        </div>

                    </div>
                </div>
            </UCard>
        </div>
    </Default>
    <USlideover v-model="isOpen"
                :ui="{width: 'lg:max-w-4xl md:max-w-lg sm:max-w-lg', background: 'bg-gradient-to-b from-rose-100 to-teal-100'}">
        <UForm
                :validate="validate"
                :state="state"
                @submit="updateFunction">
            <UCard class="flex flex-col flex-1 min-w-full min-h-full bg-gradient-to-b from-rose-100 to-teal-100"
                   :ui="{ body: { base: 'flex-1' }, divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
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
                        class="grid lg:grid-cols-6 sm:grid-cols-1 shadow-xl dark:shadow-xl dark:shadow-cyan-500/5 rounded-lg p-2 ring-3 ring-gray-100/5 dark:ring-gray-800">
                    <div class="lg:col-start-2 sm:col-start-1 lg:col-span-4 pr-5 pb-5">
                        <UFormGroup label="Nombre" name="name" class="m-5" :required="true">
                            <UInput v-model="state.name"/>
                        </UFormGroup>
                        <UFormGroup label="Descripción" name="description" class="m-5" help="Máximo 100 caracteres">
                            <UTextarea v-model="state.description" size="xl" :rows="4" :maxlength="100"/>
                        </UFormGroup>
                    </div>
                </div>
            </UCard>
        </UForm>
    </USlideover>
</template>

<style scoped></style>
