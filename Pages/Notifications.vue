<script setup>
defineProps({
    notificaciones: Array,
});
</script>

<template>
    <div>
        <h1>Notificaciones</h1>
        <ul>
            <li v-for="notification in notificaciones" :key="notification.id">
                {{ notification.data.message }}
            </li>
        </ul>
    </div>
</template>
