<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, usePage } from '@inertiajs/vue3';
import { useForm } from "@inertiajs/vue3";
import { ref } from 'vue'
import Message from "@/Pages/Messages/Message.vue";
import TextInput from "@/Components/TextInput.vue";
import { onMounted } from 'vue';

const messages = ref([]);
const flag = ref(true);
const loading = ref(false);
const page = usePage();
const form = useForm({
    userMessage: '',
})
const count = ref(1);

onMounted(async () => {
    console.log('Component is mounted!');
    const response = await axios.post('/get_history');
    console.log(response.data);
    response.data.messages.forEach((message) => {
        messages.value.push({ content: message.content, sender: message.sender });
    })
    // Your mounted logic here
});

const store = async () => {
    flag.value = false;
    loading.value = true;
    messages.value.push({ content: form.userMessage, sender: page.props.auth.user.name });
    try {
        const response = await axios.post('/chat', {
            'userMessage': form.userMessage
        })
        messages.value.push({ content: response.data.message, sender: 'ChatGPT' });
        form.userMessage = '';
    } catch (error) {
        console.log(error)
    }
    loading.value = false;
}



const deleteMessage = async (index) => {
    //get message from index
    const message = messages.value[index];
    console.log('message', message);
    //remove message from array
    messages.value.splice(index, 1);
    const response = await axios.post('/delete_message', {
        'message': message.content
    });

};
const favoriteQuestion = async () => {
    flag.value = false;
    loading.value = true;
    messages.value.push({ content: event.target.textContent, sender: page.props.auth.user.name });
    try {
        const response = await axios.post('/chat', {
            'userMessage': event.target.textContent
        })
        messages.value.push({ content: response.data.message, sender: 'ChatGPT' });
        event.target.textContent = '';
    } catch (error) {
        console.log(error)
    }
    loading.value = false;
}
</script>

<template>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-xl font-semibold leading-tight text-gray-800 dark:text-gray-200">Dashboard</h2>
        </template>

        <div class="h-full py-12">
            <div class="h-full mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="flex flex-col h-full bg-white rounded-md dark:bg-gray-800">
                    <div class="flex items-center justify-between px-4 py-3 lg:px-6">
                        <div class="relative flex items-center space-x-2">
                            <span class="relative bottom-0 right-0 w-2 h-2 bg-green-500 rounded-full">&nbsp;</span>
                            <h2 class="text-base font-semibold text-gray-800 dark:text-white">ChatGPT is online</h2>
                        </div>
                    </div>
                    <div
                        class="flex flex-col px-4 pb-8 space-y-4 border-t border-gray-200 grow lg:px-6 dark:border-gray-700">
                        <div class="flex flex-col min-h-0 space-y-4 overflow-y-auto grow basis-0">
                            <Message v-for="(message, index) in messages" :key="message.id" :message="message"
                                @deleteMessage="deleteMessage(index)" />
                        </div>

                        <div class="flex items-center pt-3" v-show="flag">
                            <div
                                class="absolute bottom-full left-0 mb-4 flex w-full grow gap-2 px-1 pb-1 sm:px-2 sm:pb-0 md:static md:mb-0 md:max-w-none">
                                <div class="grid w-full grid-flow-row grid-cols-[repeat(auto-fit,minmax(250px,1fr))] gap-2">
                                    <div class="flex flex-col gap-2">
                                        <span data-projection-id="158" style="opacity: 1; transform: none;"
                                            class="border rounded-lg">
                                            <button @click="favoriteQuestion"
                                                class="btn relative btn-neutral group w-full whitespace-nowrap rounded-xl px-4 py-3 text-left text-gray-700 dark:text-gray-300 md:whitespace-normal">
                                                <div class="flex w-full gap-2 items-center justify-center">
                                                    <div class="flex w-full items-center justify-between">
                                                        <div class="flex flex-col overflow-hidden">
                                                            <div class="truncate">Create a charter </div>
                                                            <div class="truncate font-normal opacity-50">to start a film
                                                                club </div>
                                                        </div>
                                                        <div
                                                            class="absolute bottom-0 right-0 top-0 flex items-center rounded-xl bg-gradient-to-l from-gray-50 from-[60%] pl-6 pr-4 text-gray-700 opacity-0 group-hover:opacity-100 dark:from-gray-700 dark:text-gray-200">
                                                            <span class="" data-state="closed">
                                                                <div
                                                                    class="rounded-lg bg-token-surface-primary p-1 shadow-xxs dark:shadow-none">
                                                                    <svg width="24" height="24" viewBox="0 0 24 24"
                                                                        fill="none" class="icon-sm text-token-text-primary">
                                                                        <path d="M7 11L12 6L17 11M12 18V7"
                                                                            stroke="currentColor" stroke-width="2"
                                                                            stroke-linecap="round" stroke-linejoin="round">
                                                                        </path>
                                                                    </svg>
                                                                </div>
                                                            </span>
                                                        </div>
                                                    </div>
                                                </div>
                                            </button>
                                        </span>
                                        <span data-projection-id="159" style="opacity: 1; transform: none;"
                                            class="border rounded-lg">
                                            <button @click="favoriteQuestion"
                                                class="btn relative btn-neutral group w-full whitespace-nowrap rounded-xl px-4 py-3 text-left text-gray-700 dark:text-gray-300 md:whitespace-normal">
                                                <div class="flex w-full gap-2 items-center justify-center">
                                                    <div class="flex w-full items-center justify-between">
                                                        <div class="flex flex-col overflow-hidden">
                                                            <div class="truncate">Suggest some names </div>
                                                            <div class="truncate font-normal opacity-50">for my
                                                                cafe-by-day, bar-by-night business</div>
                                                        </div>
                                                        <div
                                                            class="absolute bottom-0 right-0 top-0 flex items-center rounded-xl bg-gradient-to-l from-gray-50 from-[60%] pl-6 pr-4 text-gray-700 opacity-0 group-hover:opacity-100 dark:from-gray-700 dark:text-gray-200">
                                                            <span class="" data-state="closed">
                                                                <div
                                                                    class="rounded-lg bg-token-surface-primary p-1 shadow-xxs dark:shadow-none">
                                                                    <svg width="24" height="24" viewBox="0 0 24 24"
                                                                        fill="none" class="icon-sm text-token-text-primary">
                                                                        <path d="M7 11L12 6L17 11M12 18V7"
                                                                            stroke="currentColor" stroke-width="2"
                                                                            stroke-linecap="round" stroke-linejoin="round">
                                                                        </path>
                                                                    </svg>
                                                                </div>
                                                            </span>
                                                        </div>
                                                    </div>
                                                </div>
                                            </button>
                                        </span>
                                    </div>
                                    <div class="flex flex-col gap-2">
                                        <span data-projection-id="160" style="opacity: 1; transform: none;"
                                            class="border rounded-lg">
                                            <button @click="favoriteQuestion"
                                                class="btn relative btn-neutral group w-full whitespace-nowrap rounded-xl px-4 py-3 text-left text-gray-700 dark:text-gray-300 md:whitespace-normal">
                                                <div class="flex w-full gap-2 items-center justify-center">
                                                    <div class="flex w-full items-center justify-between">
                                                        <div class="flex flex-col overflow-hidden">
                                                            <div class="truncate">Make up a story </div>
                                                            <div class="truncate font-normal opacity-50">about Sharky, a
                                                                tooth-brushing shark superhero</div>
                                                        </div>
                                                        <div
                                                            class="absolute bottom-0 right-0 top-0 flex items-center rounded-xl bg-gradient-to-l from-gray-50 from-[60%] pl-6 pr-4 text-gray-700 opacity-0 group-hover:opacity-100 dark:from-gray-700 dark:text-gray-200">
                                                            <span class="" data-state="closed">
                                                                <div
                                                                    class="rounded-lg bg-token-surface-primary p-1 shadow-xxs dark:shadow-none">
                                                                    <svg width="24" height="24" viewBox="0 0 24 24"
                                                                        fill="none" class="icon-sm text-token-text-primary">
                                                                        <path d="M7 11L12 6L17 11M12 18V7"
                                                                            stroke="currentColor" stroke-width="2"
                                                                            stroke-linecap="round" stroke-linejoin="round">
                                                                        </path>
                                                                    </svg>
                                                                </div>
                                                            </span>
                                                        </div>
                                                    </div>
                                                </div>
                                            </button>
                                        </span>
                                        <span data-projection-id="161" style="opacity: 1; transform: none;"
                                            class="border rounded-lg">
                                            <button @click="favoriteQuestion"
                                                class="btn relative btn-neutral group w-full whitespace-nowrap rounded-xl px-4 py-3 text-left text-gray-700 dark:text-gray-300 md:whitespace-normal">
                                                <div class="flex w-full gap-2 items-center justify-center">
                                                    <div class="flex w-full items-center justify-between">
                                                        <div class="flex flex-col overflow-hidden">
                                                            <div class="truncate">Explain superconductors </div>
                                                            <div class="truncate font-normal opacity-50">like I'm five
                                                                years old</div>
                                                        </div>
                                                        <div
                                                            class="absolute bottom-0 right-0 top-0 flex items-center rounded-xl bg-gradient-to-l from-gray-50 from-[60%] pl-6 pr-4 text-gray-700 opacity-0 group-hover:opacity-100 dark:from-gray-700 dark:text-gray-200">
                                                            <span class="" data-state="closed">
                                                                <div
                                                                    class="rounded-lg bg-token-surface-primary p-1 shadow-xxs dark:shadow-none">
                                                                    <svg width="24" height="24" viewBox="0 0 24 24"
                                                                        fill="none" class="icon-sm text-token-text-primary">
                                                                        <path d="M7 11L12 6L17 11M12 18V7"
                                                                            stroke="currentColor" stroke-width="2"
                                                                            stroke-linecap="round" stroke-linejoin="round">
                                                                        </path>
                                                                    </svg>
                                                                </div>
                                                            </span>
                                                        </div>
                                                    </div>
                                                </div>
                                            </button>
                                        </span>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="flex items-center pt-3">
                            <img src="images/user.jpg" class="w-12 h-12 mr-3 rounded-full"
                                :alt="page.props.auth.user.name" />
                            <div class="flex-1">
                                <form @submit.prevent="store">
                                    <TextInput v-model="form.userMessage" type="text"
                                        class="w-full px-4 py-3 text-sm placeholder-gray-400 border border-gray-200 rounded-md"
                                        placeholder="Send a message" :disabled="loading === true" />
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
