<template>
    <Head>
        <title>{{ service.title }} | ANTASUS Infra</title>
        <meta name="description" :content="service.description" />
        <link
            rel="canonical"
            :href="`https://www.antasus.de/leistungen/${service.slug}`"
        />
    </Head>

    <GuestLayout :serviceArea="'dienstleistungen'">
        <!-- Header -->
        <section
            class="w-full px-4 text-center bg-gradient-to-br from-antasus-primary via-teal-600 to-antasus-dark/90 backdrop-blur-md"
        >
            <div class="max-w-4xl py-20 mx-auto text-white">
                <h1
                    class="mb-4 text-4xl font-extrabold md:text-5xl drop-shadow-lg"
                >
                    {{ service.title }}
                </h1>
                <p class="text-lg md:text-xl text-white/90 drop-shadow-sm">
                    {{ service.description }}
                </p>
            </div>
        </section>

        <!-- Items Grid -->
        <section class="py-20 bg-white">
            <div class="max-w-6xl px-4 mx-auto">
                <div class="grid gap-8 md:grid-cols-2">
                    <div
                        v-for="item in service.items"
                        :key="item.id"
                        @click="showModal(item)"
                        class="overflow-hidden transition border border-gray-200 shadow cursor-pointer rounded-xl hover:shadow-lg"
                    >
                        <img
                            v-if="item.image_url"
                            :src="item.image_url"
                            :alt="item.title"
                            class="object-cover w-full h-48"
                        />
                        <div class="p-5">
                            <h3 class="mb-2 text-xl font-bold text-gray-900">
                                {{ item.title }}
                            </h3>
                            <p class="text-gray-600 line-clamp-3">
                                {{ item.description }}
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Modal -->
        <Transition name="fade-slide">
            <div
                v-if="selectedItem"
                class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm"
                @click.self="closeModal"
            >
                <div
                    class="relative w-full max-w-2xl p-6 mx-4 bg-white rounded-lg shadow-xl animate-fade-in-up"
                >
                    <button
                        @click="closeModal"
                        class="absolute text-gray-400 top-3 right-3 hover:text-gray-600"
                        aria-label="Schließen"
                    >
                        &times;
                    </button>

                    <h2 class="mb-4 text-2xl font-bold text-gray-900">
                        {{ selectedItem.title }}
                    </h2>

                    <img
                        v-if="selectedItem.image_url"
                        :src="selectedItem.image_url"
                        :alt="selectedItem.title"
                        class="object-cover w-full h-64 mb-4 rounded"
                    />

                    <p class="text-gray-700 whitespace-pre-line">
                        {{ selectedItem.description }}
                    </p>

                    <div class="mt-6 text-right">
                        <Link
                            href="/kontakt"
                            class="inline-block px-6 py-3 text-white rounded shadow bg-antasus-primary hover:bg-antasus-dark"
                        >
                            Projekt anfragen
                        </Link>
                    </div>
                </div>
            </div>
        </Transition>
    </GuestLayout>
</template>

<script setup>
import GuestLayout from "@/Layouts/GuestLayout.vue";
import { Head, Link, usePage, router } from "@inertiajs/vue3";
import { ref, onMounted, onUnmounted } from "vue";

const props = defineProps({
    service: Object,
});

const selectedItem = ref(null);
const page = usePage();

function showModal(item) {
    selectedItem.value = item;
    router.push({
        replace: false,
        preserveScroll: true,
        preserveState: true,
        url: `/leistungen/${props.service.slug}/${item.slug}/${item.id}`,
    });
}

function closeModal() {
    selectedItem.value = null;
    router.push({
        replace: false,
        preserveScroll: true,
        preserveState: true,
        url: `/leistungen/${props.service.slug}`,
    });
}

function handleEscape(event) {
    if (event.key === "Escape" && selectedItem.value) {
        closeModal();
    }
}

onMounted(() => {
    document.addEventListener("keydown", handleEscape);

    const segments = page.url.split("/");
    const itemId = segments.includes("item")
        ? segments[segments.indexOf("item") + 1]
        : null;
    if (itemId) {
        const item = props.service.items.find(
            (i) => i.id.toString() === itemId
        );
        if (item) {
            selectedItem.value = item;
        }
    }
});

onUnmounted(() => {
    document.removeEventListener("keydown", handleEscape);
});
</script>

<style scoped>
.fade-slide-enter-active,
.fade-slide-leave-active {
    transition: all 0.3s ease;
}
.fade-slide-enter-from,
.fade-slide-leave-to {
    opacity: 0;
    transform: translateY(20px);
}
.line-clamp-3 {
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
</style>
