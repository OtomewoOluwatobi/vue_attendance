<script setup>
import { FilterMatchMode, FilterOperator } from '@primevue/core/api';
import { onBeforeMount, ref } from 'vue';
import axios from 'axios';

const permissionDialog = ref(false);
const permission = ref({});
const submitted = ref(false);
const permissions = ref([]);
const filters1 = ref(null);
const loading1 = ref(false);

const capitalizeFirstChar = (str) => {
    return str
        .split(' ')
        .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
        .join(' ');
};

onBeforeMount(() => {
    initFilters1();
    fetchPermissions();
});

function openNew() {
    permission.value = {};
    submitted.value = false;
    permissionDialog.value = true;
}

function editPermission(permissionData) {
    permission.value = { ...permissionData };
    permissionDialog.value = true;
}

function initFilters1() {
    filters1.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS },
        name: { operator: FilterOperator.AND, constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }] },
        'roles.name': { operator: FilterOperator.AND, constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }] }
    };
}

async function fetchPermissions() {
    try {
        loading1.value = true;
        const response = await axios.get('https://dev.hinvites.com/api/admin/access_control/permissions');
        permissions.value = response.data.permissions;
    } catch (error) {
        console.error('Error fetching permissions:', error);
    } finally {
        loading1.value = false;
    }
}
</script>

<template>
    <div>
        <div class="card">
            <Toolbar class="mb-6">
                <template #start>
                    <Button label="New Permission" icon="pi pi-plus" severity="secondary" class="mr-2" @click="openNew" />
                </template>
            </Toolbar>

            <h2 class="font-semibold text-xl mb-4">All Permissions</h2>

            <DataTable
                :value="permissions"
                :paginator="true"
                :rows="10"
                :rowsPerPageOptions="[5, 10, 25]"
                :rowHover="true"
                v-model:filters="filters1"
                filterDisplay="menu"
                :loading="loading1"
                :globalFilterFields="['name', 'roles.name']"
                showGridlines
            >
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

                <template #empty>No permissions found.</template>
                <template #loading>Loading permissions data. Please wait...</template>

                <Column field="name" header="Name" style="min-width: 12rem">
                    <template #body="{ data }">{{ data.name }}</template>
                </Column>

                <Column field="users" header="Users" style="min-width: 12rem">
                    <template #body="{ data }">{{ data.users_count }}</template>
                </Column>

                <Column field="roles" header="Roles" style="min-width: 14rem">
                    <template #body="{ data }">
                        <div v-if="data.roles && data.roles.length">
                            <Tag v-for="role in data.roles.slice(0, 5)" :key="role.id" class="mr-2 mb-2" :severity="role.name === 'manager' ? 'secondary' : 'primary'">
                                {{ role.name }}
                            </Tag>
                            <span class="mr-2 mb-2">...</span>
                        </div>
                        <div v-else>
                            <Tag class="mr-2 mb-2" severity="warn">unassigned</Tag>
                        </div>
                    </template>
                </Column>

                <Column field="actions" header="Actions" style="min-width: 4rem">
                    <template #body="{ data }">
                        <div class="flex flex-wrap gap-2">
                            <Button icon="pi pi-eye" outlined rounded severity="info" @click="editPermission(data)" />
                            <Button icon="pi pi-times-circle" outlined rounded severity="danger" />
                        </div>
                    </template>
                </Column>
            </DataTable>
        </div>

        <Dialog v-model:visible="permissionDialog" :style="{ width: '450px' }" :header="capitalizeFirstChar(permission.name ? permission.name + ' Permission' : 'Add New Permissions')" modal>
            <div class="flex flex-col gap-6">
                <div>
                    <label for="name" class="block font-bold mb-3">
                        {{ capitalizeFirstChar(permission.name ? 'Associated Roles' : 'Names') }}
                    </label>
                    <div v-if="permission.roles && permission.roles.length > 0">
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
                <Button :label="permission.name ? 'Close' : 'Save'" :icon="permission.name ? 'pi pi-times' : 'pi pi-check'" />
            </template>
        </Dialog>
    </div>
</template>
