<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import formTicket from "@/Components/Usuario/formTicket.vue";
</script>

<template>
    <AppLayout title="Crear Ticket">
        <div class="py-2">
            <formTicket endpoint="/user-tickets/store" />
        </div>
    </AppLayout>
</template>
