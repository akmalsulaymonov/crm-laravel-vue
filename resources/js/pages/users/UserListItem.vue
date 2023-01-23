<script setup>
    import { ref } from 'vue';
    import { formatDate } from '../../helper.js';
    import { useToastr } from '../../toastr.js';
    import axios from 'axios';

    const toastr = useToastr();
    
    const props = defineProps({
        user: Object,
        index: Number,
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

    const editUser = (user) => {
        emit('editUser', user);
    }

</script>

<template>
    <tr>
        <td>{{ index + 1 }}</td>
        <td>{{ user.name }}</td>
        <td>{{ user.email }}</td>
        <td>{{ formatDate(user.created_at) }}</td>
        <td>{{ user.role }}</td>
        <td>
            <a href="#" @click.prevent="editUser(user)"><i class="fa fa-edit"></i></a>
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