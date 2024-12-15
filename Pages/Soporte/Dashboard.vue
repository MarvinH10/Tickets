<script setup>
import { h } from "vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import AppLayout from "@/Layouts/AppLayout.vue";
import listDashboard from "@/Components/Soporte/listDashboard.vue";
import StarMessage from "@/Components/StarMessage.vue";

const props = defineProps({
    success: String,
});

if (props.success) {
    setTimeout(() => {
        window.location.reload();
    }, 10);
}
</script>

<template>
    <AppLayout title="Dashboard">
        <div class="py-2">
            <listDashboard />
        </div>
    </AppLayout>
</template>
