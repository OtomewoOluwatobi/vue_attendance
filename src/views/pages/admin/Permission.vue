<script setup>
import { FilterMatchMode, FilterOperator } from '@primevue/core/api';
import { onBeforeMount, ref } from 'vue';
// import axios from 'axios';

const permissionDialog = ref(false);
const permission = ref({});
const submitted = ref(false);
const permissions = ref([]);
const filters1 = ref(null);

onBeforeMount(() => {
    initFilters1();
    fetchPermissions();
});

function capitalizeFirstChar(str) {
    return str
        .split(' ')
        .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
        .join(' ');
}

function openNew() {
    permission.value = {};
    submitted.value = false;
    permissionDialog.value = true;
}

function editPermission(prod) {
    permission.value = { ...prod };
    permissionDialog.value = true;
}

function initFilters1() {
    filters1.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS },
        name: { operator: FilterOperator.AND, constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }] },
        'roles.name': { operator: FilterOperator.AND, constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }] },
        representative: { value: null, matchMode: FilterMatchMode.IN }
    };
}

const fetchPermissions = async () => {
    try {
        // const response = await axios.get('http://127.0.0.1/api/admin/access_control/permissions');
        permissions.value = [
            {
                id: 1,
                name: 'create event',
                guard_name: 'api',
                created_at: '2024-07-30T16:17:23.000000Z',
                updated_at: '2024-07-30T16:17:23.000000Z',
                users_count: 0,
                roles: [
                    {
                        id: 1,
                        name: 'manager',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:24:16.000000Z',
                        updated_at: '2024-07-30T16:24:16.000000Z',
                        pivot: {
                            permission_id: 1,
                            role_id: 1
                        }
                    }
                ]
            },
            {
                id: 2,
                name: 'update event',
                guard_name: 'api',
                created_at: '2024-07-30T16:17:23.000000Z',
                updated_at: '2024-07-30T16:17:23.000000Z',
                users_count: 0,
                roles: [
                    {
                        id: 1,
                        name: 'manager',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:24:16.000000Z',
                        updated_at: '2024-07-30T16:24:16.000000Z',
                        pivot: {
                            permission_id: 2,
                            role_id: 1
                        }
                    }
                ]
            },
            {
                id: 3,
                name: 'view event',
                guard_name: 'api',
                created_at: '2024-07-30T16:17:23.000000Z',
                updated_at: '2024-07-30T16:17:23.000000Z',
                users_count: 0,
                roles: [
                    {
                        id: 1,
                        name: 'manager',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:24:16.000000Z',
                        updated_at: '2024-07-30T16:24:16.000000Z',
                        pivot: {
                            permission_id: 3,
                            role_id: 1
                        }
                    }
                ]
            },
            {
                id: 4,
                name: 'delete event',
                guard_name: 'api',
                created_at: '2024-07-30T16:17:23.000000Z',
                updated_at: '2024-07-30T16:17:23.000000Z',
                users_count: 0,
                roles: [
                    {
                        id: 1,
                        name: 'manager',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:24:16.000000Z',
                        updated_at: '2024-07-30T16:24:16.000000Z',
                        pivot: {
                            permission_id: 4,
                            role_id: 1
                        }
                    }
                ]
            }
        ];
    } catch (error) {
        console.error('Error fetching data:', error);
    }
};
</script>

<template>
    <div>
        <div class="card">
            <Toolbar class="mb-6">
                <template #start>
                    <Button label="New Permission" icon="pi pi-plus" severity="secondary" class="mr-2" @click="openNew" />
                </template>
            </Toolbar>

            <div class="font-semibold text-xl mb-4">All Permissions</div>
            <DataTable :value="permissions" :paginator="true" :rows="10" dataKey="id" :rowHover="true" v-model:filters="filters1" filterDisplay="menu" :loading="loading1" :filters="filters1" :globalFilterFields="['name', 'roles.name']" showGridlines>
                <template #header>
                    <div class="flex justify-between">
                        <IconField>
                            <InputIcon>
                                <i class="pi pi-search" />
                            </InputIcon>
                            <InputText v-model="filters1['global'].value" placeholder="Keyword Search" />
                        </IconField>
                    </div>
                </template>
                <template #empty> No role found. </template>
                <template #loading> Loading role data. Please wait. </template>

                <Column field="name" header="Name" style="min-width: 12rem">
                    <template #body="{ data }">
                        {{ data.name.toUpperCase() }}
                    </template>
                </Column>
                <Column field="users" header="Users" style="min-width: 12rem">
                    <template #body="{ data }">
                        {{ data.users_count }}
                    </template>
                </Column>
                <Column field="permissions" header="Permissions" style="min-width: 14rem">
                    <template #body="{ data }">
                        <Tag class="mr-2 mb-2" v-for="role in data.roles.slice(0, 5)" :key="role.id" :severity="role.name === 'manager' ? 'secondary' : 'primary'">
                            {{ role.name }}
                        </Tag>
                        <span class="mr-2 mb-2"> ... </span>
                    </template>
                </Column>
                <Column field="actions" header="Actions" style="min-width: 4rem">
                    <template #body="{ data }">
                        <!-- <i class="pi" :class="{ 'pi-eye text-green-500 ': data.verified, 'pi-times-circle text-red-500': !data.verified }"></i> -->
                        <div class="flex flex-wrap gap-2">
                            <Button icon="pi pi-eye" outlined rounded severity="info" @click="editPermission(data)"></Button>
                            <Button icon="pi pi-times-circle" outlined rounded severity="danger"></Button>
                        </div>
                    </template>
                </Column>
            </DataTable>
        </div>

        <Dialog v-model:visible="permissionDialog" :style="{ width: '450px' }" :header="permission.name != null ? capitalizeFirstChar(permission.name + ' permission') : capitalizeFirstChar('add new permissions')" :modal="true">
            <div class="flex flex-col gap-6">
                <div>
                    <label for="name" class="block font-bold mb-3">{{ permission.name != null ? capitalizeFirstChar('associated roles') : capitalizeFirstChar('Names') }}</label>
                    <div v-if="permission && permission.roles && permission.roles.length > 0">
                        <div v-for="role in permission.roles.slice(0, 5)" :key="role.id">
                            <Textarea id="names" rows="10" required v-model="role.name" placeholder="Separate all new permissions with commas (e.g., create ticket, view ticket, etc.)" fluid />
                        </div>
                    </div>
                    <div v-else>
                        <Textarea id="names" rows="10" required placeholder="Separate all new permissions with commas (e.g., create ticket, view ticket, etc.)" fluid />
                    </div>
                    <small v-if="submitted && !permission.name" class="text-red-500">Name is required.</small>
                </div>
            </div>

            <template #footer>
                <Button :label="permission.name != null ? 'close' : 'save'" :icon="permission.name ? 'pi pi-times' : 'pi pi-check'" />
                <!-- @click="saveProduct" -->
            </template>
        </Dialog>
    </div>
</template>
