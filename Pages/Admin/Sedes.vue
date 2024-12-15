<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import listSedes from "@/Components/Admin/listSedes.vue";

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
<AppLayout v-if="$page.props.auth.user && $page.props.auth.user.rol && $page.props.auth.user.rol.name === 'Admin'" title="Sedes">
    <div class="py-2">
        <listSedes />
    </div>
</AppLayout>
</template>
