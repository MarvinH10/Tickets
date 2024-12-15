<script setup>
import { h } from "vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import AppLayout from "@/Layouts/AppLayout.vue";
import listDashboard from "@/Components/Usuario/listDashboard.vue";
import StarMessage from "@/Components/StarMessage.vue";

const props = defineProps({
    success: String,
});

if (props.success) {
    toast.success(
        {
            render: () => h(StarMessage, { message: props.success }),
        },
        {
            autoClose: 4000,
            position: "bottom-right",
            style: {
                width: "400px",
            },
            className: "border-l-4 border-red-500 p-4",
        }
    );
}
</script>

<template>
    <AppLayout v-if="$page.props.auth.user && $page.props.auth.user.rol && $page.props.auth.user.rol.name === 'Usuario'" title="Dashboard">
        <div class="py-2">
            <listDashboard />
        </div>
    </AppLayout>
</template>
