<script lang="ts" setup>
import Default from "~/layouts/Default.vue";

const router = useRouter()
const toast = useToast()

const state = reactive({
    name: "",
    description: "",
})

const postFunction = async () => {
    await useFetch('http://localhost:8000/api/assignments/', {
        method: "POST",
        params: {
            name: state.name,
            description: state.description,
            status: '1',
        },
    }).then((response) => {
        console.log(response)
        router.back()
        toast.add({title: 'Creado exitosamente'})
    })
}
</script>

<template>
    <Default>
        <Navbar />
    <div class="min-w-full min-h-full lg:p-4 sm:p-1">
        <UForm
                :state="state"
                @submit="postFunction">
            <UCard
                class="min-w-full min-h-full bg-gradient-to-b from-rose-100 to-teal-100"
                :ui="{ ring: 'ring-1 shadow-xl ring-gray-100/5 dark:ring-gray-800',}">
                <template #header>
                    <div
                            class="grid grid-cols-2 rounded-lg p-5 shadow-xl dark:shadow-xl dark:shadow-cyan-500/5 ring-1 ring-gray-100/5 dark:ring-gray-800">
                        <div class="col-start-1 col-span-1">
                            <h3 class="text-2xl font-semibold mb-4">
                            Crear Nueva Tarea
                            </h3>
                        </div>
                        <div class="col-start-2 col-span-1 grid justify-items-end">
                            <div class="flex items-center justify-between space-x-2 pr-12">
                                <UButton type="submit" size="sm" color="primary"
                                         variant="solid" label="Guardar"/>
                                <UButton size="sm" color="primary"
                                         variant="solid" label="Volver" :trailing="false"
                                         to="/assignments"/>
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
    </div>
    </Default>
</template>

<style scoped></style>
