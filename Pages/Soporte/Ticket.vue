<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import listTicket from "@/Components/Soporte/listTicket.vue";

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
    <AppLayout title="Tickets">
        <div class="py-2">
            <listTicket />
        </div>
    </AppLayout>
</template>
