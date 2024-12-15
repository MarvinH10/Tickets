<script setup>
import { ref, onMounted, watchEffect } from "vue";
import { useForm, usePage } from "@inertiajs/vue3";
import ActionMessage from "@/Components/ActionMessage.vue";
import FormSection from "@/Components/FormSection.vue";
import InputError from "@/Components/InputError.vue";
import InputLabel from "@/Components/InputLabel.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import axios from "axios";

const { props } = usePage();

const sedes = ref([]);

const form = useForm({
    _method: "PUT",
    sed_id: props.user_sede_id || null,
});

const fetchSedes = async () => {
    try {
        const response = await axios.get("/profile-sedes");

        if (response.data && Array.isArray(response.data.data)) {
            sedes.value = response.data.data
                .filter((sede) => sede.sed_activo)
                .map((sede) => ({
                    id: sede.id,
                    sed_nombre: sede.sed_nombre,
                }));

            form.sed_id = response.data.user_sede_id || null;
        } else {
            console.error("La respuesta no tiene la estructura esperada.");
            toast.error("Error al cargar las sedes. Estructura inesperada.", {
                autoClose: 3000,
                position: "bottom-right",
                style: { width: "400px" },
                className: "border-l-4 border-red-500 p-2",
            });
        }
    } catch (error) {
        console.error("Error al cargar las sedes:", error);
        toast.error("No se pudieron cargar las sedes.", {
            autoClose: 3000,
            position: "bottom-right",
            style: { width: "400px" },
            className: "border-l-4 border-red-500 p-2",
        });
    }
};

onMounted(() => {
    fetchSedes();
});

watchEffect(() => {
    form.sed_id = props.user_sede_id || null;
});

const updateSede = () => {
    if (form.sed_id) {
        form.post(route("actualizar-sede", { id: form.sed_id }), {
            preserveScroll: true,
            onSuccess: () => {
                toast.success("Sede actualizada correctamente.", {
                    autoClose: 3000,
                    position: "bottom-right",
                    style: { width: "400px" },
                    className: "border-l-4 border-green-500 p-2",
                });
            },
            onError: (errors) => {
                toast.error("Error al actualizar la sede.", {
                    autoClose: 3000,
                    position: "bottom-right",
                    style: { width: "400px" },
                    className: "border-l-4 border-red-500 p-2",
                });
            },
        });
    } else {
        toast.error("Por favor seleccione una sede.", {
            autoClose: 3000,
            position: "bottom-right",
            style: { width: "400px" },
            className: "border-l-4 border-red-500 p-2",
        });
    }
};
</script>

<template>
    <FormSection @submitted="updateSede">
        <template #title>Cambiar Sede</template>
        <template #description>
            Seleccione su sede para visualizar los tickets correspondientes.
        </template>

        <template #form>
            <div class="col-span-6 sm:col-span-4">
                <InputLabel for="sed_id" value="Sede" />
                <select
                    id="sed_id"
                    v-model="form.sed_id"
                    class="mt-1 block w-full border-gray-300 focus:border-[#2EBAA1] focus:ring-[#2EBAA1] focus:outline-none rounded"
                    required
                >
                    <option value="" disabled>Seleccione su sede</option>
                    <option
                        v-for="sede in sedes"
                        :key="sede.id"
                        :value="sede.id"
                    >
                        {{ sede.sed_nombre }}
                    </option>
                </select>
                <InputError :message="form.errors.sed_id" class="mt-2" />
            </div>
        </template>

        <template #actions>
            <ActionMessage :on="form.recentlySuccessful" class="me-3">
                Actualizado.
            </ActionMessage>
            <PrimaryButton :disabled="form.processing">
                Actualizar
            </PrimaryButton>
        </template>
    </FormSection>
</template>
