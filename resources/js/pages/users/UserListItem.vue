<script setup>
    import { ref } from 'vue';
    import { formatDate } from '../../helper.js';
    import { useToastr } from '../../toastr.js';
    import axios from 'axios';

    const toastr = useToastr();
    
    const props = defineProps({
        user: Object,
        index: Number,
        selectAll: Boolean
    });

    const emit = defineEmits(['userDeleted', 'editUser']);

    const userID = ref(null);

    const deleteUserForm = (user) => {
        userID.value = user.id;
        $('#userDeleteFormModal').modal('show');
    }

    const deleteUser = () => {
        axios.delete(`/api/users/${userID.value}`)
            .then(() => {
                $('#userDeleteFormModal').modal('hide');                
                toastr.success('User deleted successfully!');
                emit('userDeleted', userID.value);
            });
    }

    const roles = ref([
        {
            name: 'ADMIN',
            value: 1
        },
        {
            name: 'USER',
            value: 2,
        }
    ]);

    const changeRole = (user, role) => {
        axios.patch(`/api/users/${user.id}/change-role`, {
            role: role,            
        })
        .then(() => {
            toastr.success('Role changed successfully!');
        });
    }

    const toggleSelection = () => {
        emit('toggleSelection', props.user);
    }

</script>

<template>
    <tr>
        <td><input type="checkbox" :checked="selectAll" @change="toggleSelection" /></td>
        <td>{{ index + 1 }}</td>
        <td>{{ user.name }}</td>
        <td>{{ user.email }}</td>
        <td>{{ formatDate(user.created_at) }}</td>
        <td>
            <select class="form-control" @change="changeRole(user, $event.target.value)">
                <option v-for="role in roles" :value="role.value" :selected="(user.role === role.name)">{{ role.name }}</option>
            </select>
        </td>
        <td>
            <a href="#" @click.prevent="$emit('editUser', user)"><i class="fa fa-edit"></i></a>
            <a href="#" @click.prevent="deleteUserForm(user)"><i class="fa fa-trash text-danger ml-2"></i></a>
        </td>
    </tr>

    <!-- Modal delete -->
    <div class="modal fade" id="userDeleteFormModal" data-backdrop="static" tabindex="-1" role="dialog"
        aria-labelledby="userFormModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="userFormModalLabel">
                        <span>Delete user</span>
                    </h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <h5>Are you sure you want to delete this user?</h5>
                </div>  
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
                    <button @click.prevent="deleteUser" type="button" class="btn btn-primary">Delete user</button>
                </div>            
            </div>
        </div>
    </div>
</template>