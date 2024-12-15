<script setup>
import { Head, Link, useForm } from "@inertiajs/vue3";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import AuthenticationCard from "@/Components/AuthenticationCard.vue";
import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.vue";
import Checkbox from "@/Components/Checkbox.vue";
import InputError from "@/Components/InputError.vue";
import InputLabel from "@/Components/InputLabel.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import TextInput from "@/Components/TextInput.vue";

const props = defineProps({
    canResetPassword: Boolean,
    status: String,
    error: String,
});

const form = useForm({
    email: "",
    password: "",
    remember: false,
});

if (props.status) {
    toast.success(props.status, {
        autoClose: 4000,
        position: "bottom-right",
        style: {
            width: "400px",
        },
        className: "border-l-4 border-green-500 p-4",
    });
}

if (props.error) {
    toast.error(props.error, {
        autoClose: 4000,
        position: "bottom-right",
        style: {
            width: "400px",
        },
        className: "border-l-4 border-red-500 p-4",
    });
}

const submit = () => {
    form.transform((data) => ({
        ...data,
        remember: form.remember ? "on" : "",
    })).post(route("login"), {
        onFinish: () => form.reset("password"),
    });
};

function redirectToGoogle() {
    window.location.href = route("google");
}
</script>

<template>
    <Head title="Inicio de sesión" />

    <AuthenticationCard>
        <template #logo>
            <AuthenticationCardLogo />
        </template>
        <div class="pb-3 font-semibold text-center text-gray-800 uppercase">
            Iniciar Sesión
        </div>
        <div class="pb-5 text-sm text-center text-gray-600">
            ¿Aun no tienes una cuenta?
            <Link
                :href="route('register')"
                class="font-semibold text-gray-800 hover:text-[#2EBAA1] hover:underline"
            >
                Registrate aquí
            </Link>
        </div>

        <div class="flex justify-center mb-4">
            <button
                type="button"
                @click="redirectToGoogle"
                class="inline-flex items-center justify-center w-full px-4 py-2 text-xs font-semibold text-gray-800 uppercase transition duration-150 ease-in-out bg-white border border-gray-300 hover:bg-gray-100 active:bg-gray-200 disabled:opacity-25"
            >
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    x="0px"
                    y="0px"
                    width="18"
                    height="18"
                    viewBox="0 0 48 48"
                    class="mr-2"
                >
                    <path
                        fill="#FFC107"
                        d="M43.611,20.083H42V20H24v8h11.303c-1.649,4.657-6.08,8-11.303,8c-6.627,0-12-5.373-12-12c0-6.627,5.373-12,12-12c3.059,0,5.842,1.154,7.961,3.039l5.657-5.657C34.046,6.053,29.268,4,24,4C12.955,4,4,12.955,4,24c0,11.045,8.955,20,20,20c11.045,0,20-8.955,20-20C44,22.659,43.862,21.35,43.611,20.083z"
                    ></path>
                    <path
                        fill="#FF3D00"
                        d="M6.306,14.691l6.571,4.819C14.655,15.108,18.961,12,24,12c3.059,0,5.842,1.154,7.961,3.039l5.657-5.657C34.046,6.053,29.268,4,24,4C16.318,4,9.656,8.337,6.306,14.691z"
                    ></path>
                    <path
                        fill="#4CAF50"
                        d="M24,44c5.166,0,9.86-1.977,13.409-5.192l-6.19-5.238C29.211,35.091,26.715,36,24,36c-5.202,0-9.619-3.317-11.283-7.946l-6.522,5.025C9.505,39.556,16.227,44,24,44z"
                    ></path>
                    <path
                        fill="#1976D2"
                        d="M43.611,20.083H42V20H24v8h11.303c-0.792,2.237-2.231,4.166-4.087,5.571c0.001-0.001,0.002-0.001,0.003-0.002l6.19,5.238C36.971,39.205,44,34,44,24C44,22.659,43.862,21.35,43.611,20.083z"
                    ></path>
                </svg>
                Iniciar sesión con Google
            </button>
        </div>

        <div class="flex items-center justify-center my-4">
            <hr class="w-full border-t border-gray-300" />
            <span class="px-3 text-gray-500">o</span>
            <hr class="w-full border-t border-gray-300" />
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

            <div class="mt-4">
                <InputLabel for="password" value="Contraseña" />
                <TextInput
                    id="password"
                    v-model="form.password"
                    type="password"
                    class="block w-full mt-1"
                    required
                    autocomplete="current-password"
                />
                <InputError class="mt-2" :message="form.errors.password" />
            </div>

            <div class="block mt-4">
                <label class="flex items-center">
                    <Checkbox v-model:checked="form.remember" name="remember" />
                    <span class="text-sm text-gray-600 ms-2"
                        >Mantener sesión activa</span
                    >
                </label>
            </div>

            <div class="flex items-center justify-between mt-4">
                <Link
                    :href="route('password.request')"
                    class="text-sm font-semibold text-gray-800 hover:text-[#2EBAA1] hover:underline"
                >
                    He olvidado mí contraseña
                </Link>
                <PrimaryButton
                    class="ms-4"
                    :class="{ 'opacity-25': form.processing }"
                    :disabled="form.processing"
                >
                    Iniciar Sesión
                </PrimaryButton>
            </div>
        </form>
    </AuthenticationCard>
</template>
