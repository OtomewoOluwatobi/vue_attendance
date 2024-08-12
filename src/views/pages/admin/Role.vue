<script setup>
import { FilterMatchMode, FilterOperator } from '@primevue/core/api';
import { onBeforeMount, ref } from 'vue';
// import axios from 'axios';

const filters1 = ref(null);
const loading1 = ref(null);

const permissionsList = ref([]);
const permissionsValue = ref(null);

const roleDialog = ref(false);
const role = ref({});

const roles = ref([]);

function capitalizeFirstChar(str) {
    return str
        .split(' ')
        .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
        .join(' ');
}

function showRole(prod) {
    role.value = { ...prod };
    roleDialog.value = true;
}
function editRole(prod) {
    role.value = { ...prod };
}

onBeforeMount(() => {
    initFilters1();
    fetchRolesPermissions();
});

function initFilters1() {
    filters1.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS },
        name: { operator: FilterOperator.AND, constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }] },
        'permissions.name': { operator: FilterOperator.AND, constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }] },
        representative: { value: null, matchMode: FilterMatchMode.IN }
    };
}

const fetchRolesPermissions = async () => {
    try {
        // const response = await axios.get('http://127.0.0.1/api/admin/access_control/roles/create');
        // permissionsList.value = response.data.permissions;
        // roles.value = response.data.roles;
        permissionsList.value = [
            {
                name: 'create event',
                id: 1
            },
            {
                name: 'delete event',
                id: 4
            },
            {
                name: 'update event',
                id: 2
            },
            {
                name: 'view event',
                id: 3
            }
        ];
        roles.value = [
            {
                id: 1,
                name: 'manager',
                guard_name: 'api',
                created_at: '2024-07-30T16:24:16.000000Z',
                updated_at: '2024-07-30T16:24:16.000000Z',
                users_count: 0,
                permissions: [
                    {
                        id: 1,
                        name: 'create event',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:17:23.000000Z',
                        updated_at: '2024-07-30T16:17:23.000000Z',
                        pivot: {
                            role_id: 1,
                            permission_id: 1
                        }
                    },
                    {
                        id: 2,
                        name: 'update event',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:17:23.000000Z',
                        updated_at: '2024-07-30T16:17:23.000000Z',
                        pivot: {
                            role_id: 1,
                            permission_id: 2
                        }
                    },
                    {
                        id: 3,
                        name: 'view event',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:17:23.000000Z',
                        updated_at: '2024-07-30T16:17:23.000000Z',
                        pivot: {
                            role_id: 1,
                            permission_id: 3
                        }
                    },
                    {
                        id: 4,
                        name: 'delete event',
                        guard_name: 'api',
                        created_at: '2024-07-30T16:17:23.000000Z',
                        updated_at: '2024-07-30T16:17:23.000000Z',
                        pivot: {
                            role_id: 1,
                            permission_id: 4
                        }
                    }
                ]
            },
            {
                id: 2,
                name: 'Users',
                guard_name: '',
                created_at: null,
                updated_at: null,
                users_count: 0,
                permissions: []
            }
        ];
    } catch (error) {
        console.error('Error fetching data:', error);
    }
};
</script>

<template>
    <div class="flex flex-col md:flex-row gap-6">
        <div class="md:w-1/2">
            <div class="card gap-4 w-full">
                <div class="font-semibold text-xl">Create New {{ pageId }}</div>
                <div class="flex flex-col grow basis-0 gap-2">
                    <label for="role_name">Role Name</label>
                    <InputText class="text-lg" id="role_name" type="text" />
                </div>

                <form method="post">
                    <div class="flex flex-col grow mt-4 basis-0 gap-2">
                        <label for="permission_ids">Select Permissions</label>
                        <MultiSelect v-model="permissionsValue" id="permission_ids" :options="permissionsList" optionLabel="name" placeholder="Select Permissions" :filter="true">
                            <template #value="slotProps">
                                <div class="inline-flex items-center py-1 px-2 bg-primary text-primary-contrast rounded-border mr-2" v-for="option of slotProps.value" :key="option.code">
                                    <div>{{ capitalizeFirstChar(option.name) }}</div>
                                </div>
                                <template v-if="!slotProps.value || slotProps.value.length === 0">
                                    <div class="p-1">Select Permissions</div>
                                </template>
                            </template>
                            <template #option="slotProps">
                                <div class="flex items-center">
                                    <div>{{ capitalizeFirstChar(slotProps.option.name) }}</div>
                                </div>
                            </template>
                        </MultiSelect>
                    </div>

                    <div class="flex flex-wrap grow mt-4 gap-2">
                        <Button type="submit" label="Save" :fluid="false" icon="pi pi-save" raised />
                        <Button type="reset" label="Clear" :fluid="false" severity="danger" icon="pi pi-trash" raised />
                    </div>
                </form>
            </div>
        </div>

        <div class="md:w-1/2">
            <div class="card gap-4 w-full">
                <div class="font-semibold text-xl">Edit Role</div>
                <div class="flex flex-col grow basis-0 gap-2">
                    <label for="role_name">Role Name</label>
                    <InputText class="text-lg" v-model="role.name" id="role_name" type="text" readonly="yed" />
                </div>

                <div class="flex flex-col grow mt-4 basis-0 gap-2">
                    <label for="old_permission_ids">Existing Permissions</label>
                    <MultiSelect v-model="multiselectValue" :options="multiselectValues" optionLabel="name" placeholder="Select Countries" :filter="true">
                        <template #value="slotProps">
                            <div class="inline-flex items-center py-1 px-2 bg-primary text-primary-contrast rounded-border mr-2" v-for="option of slotProps.value" :key="option.code">
                                <div>{{ option.name }}</div>
                            </div>
                            <template v-if="!slotProps.value || slotProps.value.length === 0">
                                <div class="p-1">Existing Permissions</div>
                            </template>
                        </template>
                        <template #option="slotProps">
                            <div class="flex items-center">
                                <div>{{ slotProps.option.name }}</div>
                            </div>
                        </template>
                    </MultiSelect>
                </div>

                <div class="flex flex-wrap grow mt-4 gap-2">
                    <Button label="Update" :fluid="false" icon="pi pi-sync" raised />
                    <Button label="Delete" :fluid="false" severity="danger" icon="pi pi-trash" raised />
                </div>
            </div>
        </div>
    </div>

    <div class="card mt-6">
        <div class="font-semibold text-xl mb-4">All Roles</div>
        <DataTable :value="roles" :paginator="true" :rows="10" dataKey="id" :rowHover="true" v-model:filters="filters1" filterDisplay="menu" :loading="loading1" :filters="filters1" :globalFilterFields="['name', 'permissions.name']" showGridlines>
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
                    {{ capitalizeFirstChar(data.name) }}
                </template>
            </Column>
            <Column field="users" header="Users" style="min-width: 12rem">
                <template #body="{ data }">
                    {{ data.users_count }}
                </template>
            </Column>
            <Column field="permissions" header="Permissions" style="min-width: 14rem">
                <template #body="{ data }">
                    <Tag class="mr-2 mb-2" v-for="permission in data.permissions.slice(0, 5)" :key="permission.id" severity="secondary"> {{ capitalizeFirstChar(permission.name) }} </Tag>
                    <span class="mr-2 mb-2"> ... more </span>
                </template>
            </Column>
            <Column field="actions" header="Actions" style="min-width: 4rem">
                <template #body="{ data }">
                    <!-- <i class="pi" :class="{ 'pi-eye text-green-500 ': data.verif ied, 'pi-times-circle text-red-500': !data.verified }"></i> -->
                    <div class="flex flex-wrap gap-2">
                        <Button icon="pi pi-eye" outlined rounded severity="info" @click="showRole(data)"></Button>
                        <Button icon="pi pi-pencil" outlined rounded severity="primary" @click="editRole(data)"> </Button>
                        <Button icon="pi pi-times-circle" outlined rounded severity="danger"></Button>
                    </div>
                </template>
            </Column>
        </DataTable>

        <Dialog v-model:visible="roleDialog" :style="{ width: '450px' }" :header="capitalizeFirstChar(`${role.name} Role`)" :modal="true">
            <div class="flex flex-col gap-6">
                <div class="mb-4">
                    <label for="name" class="block font-bold mb-3">
                        {{ role.name ? capitalizeFirstChar('Associated Permissions') : capitalizeFirstChar('Names') }}
                    </label>
                    <Tag v-for="permission in role.permissions.slice(0, 5)" :key="permission.id" class="mr-2 mb-2" severity="secondary">
                        {{ capitalizeFirstChar(permission.name) }}
                    </Tag>
                    <small v-if="submitted && !permission.name" class="text-red-500"> Name is required. </small>
                </div>
            </div>
        </Dialog>
    </div>
</template>
