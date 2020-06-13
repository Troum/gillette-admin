<template>
    <v-card flat>
        <v-card-title>
            <v-spacer></v-spacer>
            <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Поиск"
                    single-line
                    hide-details
            ></v-text-field>
        </v-card-title>
        <v-card-text>
            <v-data-table
                    :loading="$store.getters.loading" loading-text="Загрузка... Пожалуйста, подождите"
                    :headers="headers"
                    :items="users"
                    :single-expand="singleExpand"
                    :expanded.sync="expanded"
                    :search="search"
                    show-expand
                    :fixed-header="true"
                    :items-per-page="5"
            >
                <template v-slot:top>
                    <v-toolbar flat>
                        <v-spacer></v-spacer>
                        <v-switch v-model="singleExpand" label="Режим одиночного просмотра" class="mt-2"></v-switch>
                    </v-toolbar>
                </template>
                <template v-slot:expanded-item="{ headers, item }">
                    <td class="pa-0" :colspan="headers.length">
                        <v-data-table
                                fixed-header
                                :headers="infoHeaders"
                                :items="formatInfo(item.info)"
                                hide-default-footer
                        >
                            <template v-slot:item.actions="{ item }">
                                <v-icon
                                        small
                                        class="mr-2"
                                        @click="showImage(item)"
                                >
                                    mdi-image
                                </v-icon>
                            </template>
                        </v-data-table>
                    </td>
                </template>
            </v-data-table>
        </v-card-text>
    </v-card>
</template>

<script>

    export default {
        name: "ModerateTable",
        props: {
          status: null
        },
        data() {
            return {
                expanded: [],
                singleExpand: false,
                headers: [
                    {
                        text: 'Фамилия',
                        align: 'center',
                        sortable: true,
                        value: 'surname',
                    },
                    { text: 'Имя', value: 'name',align: 'center', sortable: true },
                    { text: 'Отчество', value: 'second_name',align: 'center', sortable: true },
                    { text: 'Адрес', value: 'address',align: 'center', sortable: true },
                    { text: 'E-mail', value: 'email',align: 'center', sortable: true },
                    { text: 'Номер телефона', value: 'phone',align: 'center', sortable: true },
                    { text: 'Оператор', value: 'operator',align: 'center', sortable: true },
                    { text: '', value: 'data-table-expand', sortable: true },
                ],
                infoHeaders: [
                    {
                        text: 'Дата покупки',
                        align: 'center',
                        sortable: true,
                        value: 'bought_date',
                    },
                    { text: 'Дата регистрации', value: 'register_date',align: 'center', sortable: true },
                    { text: 'Штрихкод товара', value: 'good',align: 'center', sortable: true },
                    { text: 'Наименование товара', value: 'good_text', align: 'center', sortable: false },
                    { text: 'Количество товара', value: 'count', align: 'center', sortable: false },
                    { text: 'Магазин приобретения', value: 'shop', align: 'center', sortable: false },
                    { text: 'Действия', value: 'actions', align: 'center', sortable: false }
                ],
                users: [],
                search: ''
            }
        },
        mounted() {
            this.getUsers();
            this.$root.$on('reload-users', () => {
                this.getUsers();
            })

        },
        methods: {
            getUsers() {
                this.$store.commit('loading', true);
                setTimeout(() => {
                    this.$httpService.get(`api/v2/auth/users/${this.status}`)
                        .then(response => {
                            this.users = [...response.data.users];
                        })
                        .finally(() => {
                            this.$store.commit('loading', null);
                        })
                }, 3000)
            },
            showImage(item) {
                this.$store.commit('overlay', true);
                this.$store.commit('dialog', {status: true, data: item});
            },
            formatInfo(info) {
                let infoArr = [];
                infoArr.push(info);
                return infoArr;
            }
        }

    }
</script>

<style scoped>

</style>
