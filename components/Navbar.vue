<script lang="ts" setup>
const navigation = [
    { title: "Home", router: "/" },
    { title: "Tareas", router: "/assignments" },
    { title: "Archivadas", router: "/assignments/index-filed" },
]
</script>

<template>
    <nav class="bg-gradient-to-b from-rose-100 to-teal-100 w-full border-b md:border-0 md:static p-2">
        <div class="items-center px-4 max-w-screen-xl mx-auto md:flex md:px-8">
            <div class="flex-1 justify-self-center pb-3 mt-8 md:block md:pb-0 md:mt-0">
                <ul class="flex justify-center items-center space-x-6">
                    <li v-for="link in navigation" class="text-gray-600 text-xl hover:text-indigo-600">
                        <NuxtLink :to="link.router">
                            {{ link.title }}
                        </NuxtLink>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
</template>

<style scoped></style>
