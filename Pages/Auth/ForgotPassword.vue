<script setup>
import { ref } from "vue";
import { Head, useForm, Link } from "@inertiajs/vue3";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import AuthenticationCard from "@/Components/AuthenticationCard.vue";
import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.vue";
import InputError from "@/Components/InputError.vue";
import InputLabel from "@/Components/InputLabel.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import TextInput from "@/Components/TextInput.vue";

const message = ref("");

const form = useForm({
    email: "",
});

const submit = () => {
    form.post(route("password.email"), {
        onSuccess: () => {
            toast.success("Enlace de restablecimiento enviado.", {
                autoClose: 4000,
                position: "bottom-right",
                style: {
                    width: "400px",
                },
                className: "border-l-4 border-green-500 p-4",
            });
        },
        onError: () => {
            toast.error("Error al enviar el enlace.", {
                autoClose: 4000,
                position: "bottom-right",
                style: {
                    width: "400px",
                },
                className: "border-l-4 border-red-500 p-4",
            });
        },
    });
};
</script>

<template>
    <Head title="Olvidaste tu Contraseña" />

    <AuthenticationCard>
        <template #logo>
            <AuthenticationCardLogo />
        </template>

        <div class="mb-4 text-sm text-gray-600">
            ¿Olvidaste tu contraseña? No hay problema. Sólo déjanos saber tu
            correo electrónico dirección y le enviaremos a su mismo correo
            electrónico un enlace para restablecer la contraseña que le
            permitirá a usted poder elegir uno nuevo.
        </div>

        <div
            v-if="message"
            class="mb-4 text-sm font-medium"
            :class="
                message.includes('enviado') ? 'text-green-600' : 'text-red-600'
            "
        >
            {{ message }}
        </div>

        <form @submit.prevent="submit">
            <div>
                <InputLabel for="email" value="Correo Electrónico" />
                <TextInput
                    id="email"
                    v-model="form.email"
                    type="email"
                    class="block w-full mt-1"
                    required
                    autofocus
                    autocomplete="username"
                />
                <InputError class="mt-2" :message="form.errors.email" />
            </div>

            <div class="flex items-center justify-between mt-4">
                <Link
                    :href="route('login')"
                    class="text-sm font-semibold text-gray-800 hover:text-[#2EBAA1] hover:underline"
                >
                    Regresar a Inicio
                </Link>
                <PrimaryButton
                    :class="{ 'opacity-25': form.processing }"
                    :disabled="form.processing"
                >
                    Restablecer Contraseña
                </PrimaryButton>
            </div>
        </form>
    </AuthenticationCard>
</template>
