<template>
    <div>
        <!-- Navbar -->
        <b-navbar class="bg-bootstrap" type="dark">
            <b-container>
                <b-navbar-brand><i class="fab fa-vuejs"></i> Bootstrap Vue Demo</b-navbar-brand>
            </b-container>
        </b-navbar>

        <!-- Jumbotron + Create Button -->
        <b-jumbotron fluid>
            <template slot="header">
                <span>User Management</span>
                <div class="float-right">
                    <b-button size="lg" type="button" variant="success" v-b-modal.form_modal><i class="fas fa-plus"></i> New User</b-button>
                </div>
            </template>
        </b-jumbotron>

        <!-- Table -->
        <b-container>
            <b-table bordered striped v-bind:items="items" v-bind:fields="fields">
                <template slot="active" slot-scope="row">
                    <b-badge v-if="row.item.isActive === true" variant="success">Active</b-badge>
                    <b-badge v-if="row.item.isActive === false" variant="secondary">Inactive</b-badge>
                </template>
                <template slot="actions" slot-scope="row">
                    <b-button size="sm" variant="danger" v-on:click="deleteRow(row.item.id)"><i class="fas fa-times"></i> Delete</b-button>
                </template>
            </b-table>
        </b-container>

        <!-- Modal Form -->
        <b-modal id="form_modal" title="Create new User" header-bg-variant="dark" header-text-variant="light" ok-variant="success" v-on:ok="submitForm">
            <template slot="modal-ok"> <!-- Oder ok-title="Create User" -->
                <i class="fas fa-plus"></i> Create User
            </template>

            <b-form>
                <b-form-group label="Your Firstname:">
                    <b-form-input type="text" v-model="form.firstname" placeholder="Firstname"></b-form-input>
                </b-form-group>
                <b-form-group label="Your Lastname:">
                    <b-form-input type="text" v-model="form.lastname" placeholder="Lastname"></b-form-input>
                </b-form-group>
                <b-form-group label="Your Age:">
                    <b-form-input type="number" v-model="form.age" placeholder="Age"></b-form-input>
                </b-form-group>
                <b-form-group>
                    <b-form-checkbox v-model="form.active">Active</b-form-checkbox>
                </b-form-group>
            </b-form>
        </b-modal>
    </div>
</template>

<script>
    export default {
        data: function () {
            return {
                /**
                 * Table Column Definitions
                 */
                fields: [
                    { key: 'active', label: 'Active' },
                    { key: 'first_name', sortable: true },
                    { key: 'last_name', sortable: true },
                    { key: 'age', sortable: true },
                    { key: 'actions', label: 'Actions' }
                ],
                /**
                 * Table Raw Data
                 */
                items: [
                    { id: 1, isActive: true, age: 40, first_name: 'Jan', last_name: 'Turner' },
                    { id: 2, isActive: false, age: 21, first_name: 'Katherine', last_name: 'Stewart' },
                    { id: 3, isActive: false, age: 89, first_name: 'John', last_name: 'Henderson' },
                    { id: 4, isActive: true, age: 38, first_name: 'Anne', last_name: 'Wilson' },
                    { id: 5, isActive: true, age: 18, first_name: 'Robert', last_name: 'Pullman' },
                    { id: 6, isActive: false, age: 24, first_name: 'Stephen', last_name: 'Clarkson' },
                    { id: 7, isActive: true, age: 28, first_name: 'Caroline', last_name: 'Duncan' },
                ],
                /**
                 * Form Data Vars
                 */
                form: {
                    firstname: '',
                    lastname: '',
                    age: 0,
                    active: false
                },
            }
        },
        methods: {
            /**
             * Delete Table Row Function
             * @param delete_id
             */
            deleteRow: function ( delete_id ) {
                this.items = this.items.filter( function ( item ) {
                    return item.id !== delete_id;
                } );
            },

            /**
             * Add Table Row Function
             * @param row
             */
            addRow: function ( row ) {
                this.items.push( row );
            },

            /**
             * Returns the next auto increment id
             * @return int
             */
            getNextAutoId: function () {
                if ( this.items.length === 0 ) {
                    return 0;
                }

                return this.items[ this.items.length - 1 ].id + 1;
            },

            /**
             * Clear Form Function
             */
            clearForm: function () {
                this.form.firstname = '';
                this.form.lastname = '';
                this.form.age = 0;
                this.form.active = false;
            },

            /**
             * Form Submit Event Handler
             */
            submitForm: function () {
                this.addRow( {
                    id: this.getNextAutoId(),
                    isActive: this.form.active,
                    age: this.form.age,
                    first_name: this.form.firstname,
                    last_name: this.form.lastname
                } );

                this.clearForm();
            }
        }
    }
</script>

<style>
    .bg-bootstrap {
        background-color: rgb(86, 61, 124);
    }
</style>
