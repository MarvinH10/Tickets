<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import listCategoria from '@/Components/Admin/listCategoria.vue';

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
    <AppLayout v-if="$page.props.auth.user && $page.props.auth.user.rol && $page.props.auth.user.rol.name === 'Admin'" title="Aulas">
        <div class="py-2">
            <listCategoria/>
        </div>
    </AppLayout>
</template>