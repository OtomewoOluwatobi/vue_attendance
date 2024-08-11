<script setup>
import { ref } from 'vue';

import AppMenuItem from './AppMenuItem.vue';

const model = ref([
    {
        label: 'Home',
        items: [
            { label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/' },
            {
                label: 'Events',
                icon: 'pi pi-fw pi-calendar',
                items: [
                    {
                        label: 'Create New',
                        icon: 'pi pi-fw pi-pencil',
                        to: '/auth/login'
                    },
                    {
                        label: 'Manage Events',
                        icon: 'pi pi-fw pi-list',
                        to: '/auth/error'
                    }
                ]
            },
            {
                label: 'Tickets',
                icon: 'pi pi-fw pi-check-square',
                items: [
                    {
                        label: 'Create New',
                        icon: 'pi pi-fw pi-pencil',
                        to: '/auth/login'
                    },
                    {
                        label: 'Manage Tickets',
                        icon: 'pi pi-fw pi-list',
                        to: '/auth/error'
                    }
                ]
            },
            { label: 'Support', icon: 'pi pi-fw pi-shield', to: '/support' }
        ]
    },
    {
        label: 'Admin',
        items: [
            { label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/' },
            { label: 'Users', icon: 'pi pi-fw pi-user', to: '/users' },
            { label: 'Events', icon: 'pi pi-fw pi-calendar', to: '/events' },
            { label: 'Tickets', icon: 'pi pi-fw pi-check-square', to: '/tickets' },
            {
                label: 'Access Controls',
                icon: 'pi pi-fw pi-key',
                items: [
                    {
                        label: 'Roles',
                        icon: 'pi pi-fw pi-briefcase',
                        to: '/admin/roles'
                    },
                    {
                        label: 'Permissions',
                        icon: 'pi pi-fw pi-tags',
                        to: '/admin/permission'
                    }
                ]
            },
            { label: 'Support', icon: 'pi pi-fw pi-shield', to: '/support' }
        ]
    }
]);
</script>

<template>
    <ul class="layout-menu">
        <template v-for="(item, i) in model" :key="item">
            <app-menu-item v-if="!item.separator" :item="item" :index="i"></app-menu-item>
            <li v-if="item.separator" class="menu-separator"></li>
        </template>
    </ul>
</template>

<style lang="scss" scoped></style>
