<script setup>
import { ref, onMounted } from "vue";
import { toast } from "vue3-toastify";
import axios from "axios";
import "vue3-toastify/dist/index.css";
import AppLayout from "@/Layouts/AppLayout.vue";
import Dropdown from "@/Components/Dropdown.vue";

const props = defineProps({
    ticket: Object,
    comentarios: Array,
    success: String,
});

const ticket = ref(props.ticket);
const comentarios = ref([...props.comentarios]);
const nuevoComentario = ref("");
const archivoAdjunto = ref(null);
const error = ref(null);
const archivoSeleccionado = ref("");
const isMobile = ref(window.innerWidth <= 768);

const getInitials = (name) => {
    if (!name) return "";
    return name
        .split(" ")
        .map((n) => n[0])
        .join("")
        .toUpperCase();
};

const handleFileChange = (event) => {
    const file = event.target.files[0];
    archivoAdjunto.value = file ? file : null;

    if (file) {
        const fileType = file.type.split("/")[0];
        archivoSeleccionado.value = fileType === "image" ? "image" : "file";
    } else {
        archivoSeleccionado.value = "";
    }
};

const subirComentario = async () => {
    if (!ticket.value || !ticket.value.id) {
        error.value = "El ticket no está definido o falta su ID.";
        mostrarError(error.value);
        return;
    }

    if (!nuevoComentario.value.trim() && !archivoAdjunto.value) {
        error.value = "Debe escribir un comentario.";
        mostrarAdvertencia(error.value);
        return;
    }

    try {
        const formData = new FormData();
        formData.append("com_texto", nuevoComentario.value);

        if (archivoAdjunto.value) {
            formData.append("com_adjunto", archivoAdjunto.value);
        }

        const response = await axios.post(
            `/soporte/tickets/${ticket.value.id}/comentarios`,
            formData,
            {
                headers: {
                    "Content-Type": "multipart/form-data",
                    "X-CSRF-TOKEN": document
                        .querySelector('meta[name="csrf-token"]')
                        .getAttribute("content"),
                },
            }
        );

        if (response.status === 201 && response.data.comentario) {
            comentarios.value.push(response.data.comentario);
            nuevoComentario.value = "";
            archivoAdjunto.value = null;
            archivoSeleccionado.value = "";

            mostrarExito("Comentario registrado");
        }
    } catch (err) {
        error.value = "Error al enviar el comentario.";
        mostrarError(error.value);
        console.error(
            "Error al enviar el comentario:",
            err.response?.data?.message || err.message
        );
    }
};

const mostrarExito = (mensaje) => {
    toast.success(mensaje, {
        autoClose: 3000,
        position: "bottom-right",
        style: { width: "400px" },
        className: "border-l-4 border-green-500 p-2",
    });
};

const mostrarAdvertencia = (mensaje) => {
    toast.warn(mensaje, {
        autoClose: 3000,
        position: "bottom-right",
        style: { width: "400px" },
    });
};

const mostrarError = (mensaje) => {
    toast.error(mensaje, {
        autoClose: 3000,
        position: "bottom-right",
        style: { width: "400px" },
    });
};

const handleResize = () => {
    isMobile.value = window.innerWidth <= 768;
};

const irAtras = () => {
    window.history.back();
};

onMounted(() => {
    if (props.success) {
        mostrarExito(props.success);
    }
    window.addEventListener("resize", handleResize);
});
</script>

<template>
    <AppLayout title="Comentarios">
        <div class="p-6">
            <div class="mb-4 flex items-center">
                <button
                    @click="irAtras"
                    class="flex items-center space-x-2 text-[#2EBAA1] hover:text-[#1F9B87]"
                >
                    <i class="fas fa-arrow-left text-lg"></i>
                    <span>Regresar</span>
                </button>
            </div>
            <h1
                class="mb-6 text-sm font-bold text-gray-500 sm:text-lg md:text-xl"
            >
                Historial de Comunicaciones
            </h1>

            <div class="flex items-start mb-6">
                <div v-if="!isMobile" class="flex-shrink-0 mr-4">
                    <Dropdown align="right" width="48">
                        <template #trigger>
                            <div
                                class="flex items-center text-sm transition border-2 border-transparent rounded-full focus:outline-none"
                            >
                                <template
                                    v-if="
                                        $page.props.auth.user.profile_photo_url
                                    "
                                >
                                    <img
                                        class="w-12 h-12 rounded-full"
                                        :src="
                                            $page.props.auth.user
                                                .profile_photo_url
                                        "
                                        :alt="$page.props.auth.user.name"
                                    />
                                </template>
                                <template v-else>
                                    <div
                                        class="flex items-center justify-center w-12 h-12 font-bold text-[#2EBAA1] bg-gray-200 rounded-full"
                                    >
                                        {{
                                            getInitials(
                                                $page.props.auth.user.name
                                            )
                                        }}
                                    </div>
                                </template>
                            </div>
                        </template>
                    </Dropdown>
                </div>

                <div class="flex-grow w-full">
                    <textarea
                        v-model="nuevoComentario"
                        placeholder="Escriba un mensaje..."
                        rows="5"
                        class="w-full p-3 border border-[#2EBAA1] rounded-lg focus:ring focus:ring-opacity-100 focus:ring-[#2EBAA1]"
                    ></textarea>

                    <div
                        v-if="archivoSeleccionado"
                        class="mt-2 flex items-center"
                    >
                        <template v-if="archivoSeleccionado === 'image'">
                            <i class="fas fa-image text-[#2EBAA1] text-2xl"></i>
                        </template>
                        <template v-else>
                            <i class="fas fa-file text-[#2EBAA1] text-2xl"></i>
                        </template>
                        <span class="ml-2 text-sm text-gray-500">
                            Archivo seleccionado
                        </span>
                    </div>

                    <div class="flex justify-end items-center mt-2 space-x-2">
                        <label
                            class="cursor-pointer inline-block text-black mr-4"
                        >
                            <i class="fas fa-paperclip text-s"></i>
                            <input
                                type="file"
                                @change="handleFileChange"
                                class="hidden"
                            />
                        </label>

                        <button
                            class="bg-[#2EBAA1] text-white px-6 py-2 rounded-lg"
                            @click="subirComentario"
                        >
                            Enviar
                        </button>
                    </div>
                </div>
            </div>

            <div v-if="comentarios.length > 0">
                <div v-if="comentarios.length > 1" class="text-gray-700 mb-4">
                    {{ comentarios.length }} comentarios
                </div>

                <div v-else class="text-gray-700 mb-4">
                    {{ comentarios.length }} comentario
                </div>

                <div
                    v-for="comentario in comentarios"
                    :key="comentario.id"
                    class="flex flex-col sm:flex-row mb-6 p-4 border-b ml-0 sm:ml-16"
                >
                    <div class="flex-shrink-0 mr-4">
                        <template v-if="comentario.user?.profile_photo_url">
                            <img
                                class="w-12 h-12 rounded-full object-cover"
                                :src="comentario.user.profile_photo_url"
                                :alt="comentario.user.name"
                            />
                        </template>
                        <template v-else>
                            <div
                                class="flex items-center justify-center w-12 h-12 font-bold text-white bg-gray-500 rounded-full"
                            >
                                {{ getInitials(comentario.user?.name || "U") }}
                            </div>
                        </template>
                    </div>

                    <div class="flex-grow">
                        <h3 class="text-lg font-semibold text-[#2EBAA1]">
                            {{ comentario.user?.name || "Usuario Anónimo" }}
                        </h3>
                        <p class="text-sm text-gray-500 mb-1">
                            Publicado el:
                            {{
                                new Date(comentario.created_at).toLocaleString()
                            }}
                        </p>
                        <p class="text-gray-700">{{ comentario.com_texto }}</p>

                        <template v-if="comentario.com_adjunto">
                            <div class="flex items-center space-x-2">
                                <template
                                    v-if="
                                        ['jpg', 'jpeg', 'png', 'gif'].includes(
                                            comentario.com_adjunto
                                                .split('.')
                                                .pop()
                                                .toLowerCase()
                                        )
                                    "
                                >
                                    <i
                                        class="fas fa-image text-[#2EBAA1] text-lg"
                                    ></i>
                                </template>
                                <template v-else>
                                    <i
                                        class="fas fa-file text-[#2EBAA1] text-lg"
                                    ></i>
                                </template>
                                <a
                                    :href="`/storage/${comentario.com_adjunto}`"
                                    target="_blank"
                                    class="text-blue-600 hover:underline text-xs"
                                >
                                    Ver archivo adjunto
                                </a>
                            </div>
                        </template>
                    </div>
                </div>
            </div>

            <div v-else>
                <p class="text-gray-500 ml-16">
                    No hay comentarios disponibles para este ticket.
                </p>
            </div>
        </div>
    </AppLayout>
</template>
