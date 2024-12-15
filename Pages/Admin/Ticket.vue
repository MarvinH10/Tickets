<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import listTicket from "@/Components/Admin/listTicket.vue";

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
    <AppLayout v-if="$page.props.auth.user && $page.props.auth.user.rol && $page.props.auth.user.rol.name === 'Admin'" title="Tickets">
        <div class="py-2">
            <listTicket />
        </div>
    </AppLayout>
</template>
