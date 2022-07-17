<template>
    <v-data-table :headers="headers" :items="pessoas" sort-by="id" class="elevation-1">
        <template v-slot:top>
            <v-toolbar flat>
                <v-toolbar-title>Pessoas</v-toolbar-title>
                <v-divider class="mx-4" inset vertical></v-divider>
                <v-spacer></v-spacer>
                <v-dialog v-model="dialog" max-width="750px">
                    <template v-slot:activator="{ on, attrs }">
                        <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                            Nova Pessoa
                        </v-btn>
                    </template>
                    <v-card>
                        <v-card-title>
                            <span class="text-h5">{{ formTitle }}</span>
                        </v-card-title>

                        <v-card-text>
                            <v-container>
                                <v-row>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-text-field v-model="editedItem.id" label="Id"></v-text-field>
                                    </v-col>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-text-field v-model="editedItem.nome" label="Nome"></v-text-field>
                                    </v-col>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-text-field v-model="editedItem.idade" label="Idade"></v-text-field>
                                    </v-col>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-text-field v-model="editedItem.cidade" label="Cidade"></v-text-field>
                                    </v-col>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-text-field v-model="editedItem.email" label="Email"></v-text-field>
                                    </v-col>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-text-field v-model="editedItem.telefone" label="Telefone"></v-text-field>
                                    </v-col>

                                </v-row>
                            </v-container>
                        </v-card-text>

                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="blue darken-1" text @click="close">
                                Cancelar
                            </v-btn>
                            <v-btn color="blue darken-1" text @click="save">
                                Salvar
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
                <v-dialog v-model="dialogDelete" max-width="500px">
                    <v-card>
                        <v-card-title class="text-h5">Tem certeza?</v-card-title>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="blue darken-1" text @click="closeDelete">Cancelar</v-btn>
                            <v-btn color="blue darken-1" text @click="deleteItemConfirm">Sim</v-btn>
                            <v-spacer></v-spacer>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-toolbar>
        </template>
        <template v-slot:item.actions="{ item }">
            <v-icon small class="mr-2" @click="editItem(item)">
                mdi-pencil
            </v-icon>
            <v-icon small @click="deleteItem(item)">
                mdi-delete
            </v-icon>
        </template>
    </v-data-table>
</template>

<script>
export default {
    data: () => ({
        dialog: false,
        dialogDelete: false,
        headers: [
            { text: 'Id', align: 'start', value: 'id' },
            { text: 'Nome', value: 'nome' },
            { text: 'Idade', value: 'idade' },
            { text: 'Cidade', value: 'cidade' },
            { text: 'Email', value: 'email' },
            { text: 'Telefone', value: 'telefone' },
            { text: 'Ações', value: 'actions', sortable: false },
        ],
        pessoas: [],
        editedIndex: -1,
        editedItem: {
            id: 0,
            nome: '',
            idade: '',
            cidade: '',
            email: '',
            telefone: '',
        },
        defaultItem: {
            id: 0,
            nome: '',
            idade: '',
            cidade: '',
            email: '',
            telefone: '',
        },
    }),

    computed: {
        formTitle() {
            return this.editedIndex === -1 ? 'Nova Pessoa' : 'Editar Pessoa'
        },
    },

    watch: {
        dialog(val) {
            val || this.close()
        },
        dialogDelete(val) {
            val || this.closeDelete()
        },
    },

    created() {
        this.initialize()
    },

    methods: {
        initialize() {
            this.$api.Pessoa.GetAll().then((response) => {
                this.pessoas = response;
            })
        },

        refresh() {
            this.$api.Pessoa.GetAll().then((response) => {
                this.pessoas = response;
            })
        },

        editItem(item) {
            this.editedIndex = this.pessoas.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialog = true
        },

        deleteItem(item) {
            this.editedIndex = this.pessoas.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialogDelete = true
        },

        deleteItemConfirm() {
            this.$api.Pessoa.Delete(this.editedIndex)
            this.closeDelete()
        },

        close() {
            this.dialog = false
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem)
                this.editedIndex = -1
            })
            this.refresh()
        },

        closeDelete() {
            this.dialogDelete = false
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem)
                this.editedIndex = -1
            })
            this.refresh()
        },

        save() {
            if (this.editedIndex > -1) {
                this.$api.Pessoa.Put(this.editedIndex, this.editedItem)
            } else {
                this.$api.Pessoa.Post(this.editedItem)
            }
            this.close()
        },
    },
}
</script>