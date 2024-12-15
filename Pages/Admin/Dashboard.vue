<script setup>
import "vue3-toastify/dist/index.css";
import AppLayout from "@/Layouts/AppLayout.vue";
import listDashboard from "@/Components/Admin/listDashboard.vue";

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
<AppLayout v-if="$page.props.auth.user && $page.props.auth.user.rol && $page.props.auth.user.rol.name === 'Admin'" title="Dashboard">
    <div class="py-2">
        <listDashboard />
    </div>
</AppLayout>
</template>
