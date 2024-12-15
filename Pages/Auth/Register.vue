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

const form = useForm({
    name: "",
    apellidos: "",
    email: "",
    password: "",
    password_confirmation: "",
    terms: false,
});

const submit = () => {
    form.post(route("register"), {
        onSuccess: () => {
            toast.success("Se ha registrado correctamente, ahora seleccione la sede a la que pertenece.", {
                autoClose: 4000,
                position: "bottom-right",
                style: {
                    width: "400px",
                },
                className: "border-l-4 border-green-500 p-4",
            });
            form.reset("password", "password_confirmation");
        },
        onError: () => {
            toast.error("Ups! Algo malo sucedió", {
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
    <Head title="Registro" />

    <AuthenticationCard>
        <template #logo>
            <AuthenticationCardLogo />
        </template>
        <div class="pb-3 font-semibold text-center text-gray-800 uppercase">
            Registrarse
        </div>
        <div class="pb-5 text-sm text-center text-gray-600">
            ¿Ya tienes una cuenta?
            <Link
                :href="route('login')"
                class="font-semibold text-gray-800 hover:text-[#2EBAA1] hover:underline"
            >
                Inicia sesión aquí
            </Link>
        </div>
        <form @submit.prevent="submit">
            <div>
                <InputLabel for="name" value="Nombres" />
                <TextInput
                    id="name"
                    v-model="form.name"
                    type="text"
                    class="block w-full mt-1"
                    required
                    autofocus
                    autocomplete="name"
                />
                <InputError class="mt-2" :message="form.errors.name" />
            </div>

            <div class="mt-4">
                <InputLabel for="apellidos" value="Apellidos" />
                <TextInput
                    id="apellidos"
                    v-model="form.apellidos"
                    type="text"
                    class="block w-full mt-1"
                    required
                    autocomplete="family-name"
                />
                <InputError class="mt-2" :message="form.errors.apellidos" />
            </div>

            <div class="mt-4">
                <InputLabel for="email" value="Correo Electrónico" />
                <TextInput
                    id="email"
                    v-model="form.email"
                    type="email"
                    class="block w-full mt-1"
                    required
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
                    autocomplete="new-password"
                />
                <InputError class="mt-2" :message="form.errors.password" />
            </div>

            <div class="mt-4">
                <InputLabel
                    for="password_confirmation"
                    value="Confirmar Contraseña"
                />
                <TextInput
                    id="password_confirmation"
                    v-model="form.password_confirmation"
                    type="password"
                    class="block w-full mt-1"
                    required
                    autocomplete="new-password"
                />
                <InputError
                    class="mt-2"
                    :message="form.errors.password_confirmation"
                />
            </div>

            <div
                v-if="$page.props.jetstream.hasTermsAndPrivacyPolicyFeature"
                class="mt-4"
            >
                <InputLabel for="terms">
                    <div class="flex items-center">
                        <Checkbox
                            id="terms"
                            v-model:checked="form.terms"
                            name="terms"
                            required
                        />

                        <div class="text-xs ms-2">
                            Acepto los
                            <a
                                target="_blank"
                                :href="route('terms.show')"
                                class="text-xs text-gray-600 underline rounded-md hover:text-[#2EBAA1] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                                >Términos de servicio</a
                            >
                            y
                            <a
                                target="_blank"
                                :href="route('policy.show')"
                                class="text-xs text-gray-600 underline rounded-md hover:text-[#2EBAA1] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                                >Política de privacidad</a
                            >
                        </div>
                    </div>
                    <InputError class="mt-2" :message="form.errors.terms" />
                </InputLabel>
            </div>

            <div class="flex items-center justify-end mt-4">
                <PrimaryButton
                    class="ms-4"
                    :class="{ 'opacity-25': form.processing }"
                    :disabled="form.processing"
                >
                    Registrarme
                </PrimaryButton>
            </div>
        </form>
    </AuthenticationCard>
</template>
