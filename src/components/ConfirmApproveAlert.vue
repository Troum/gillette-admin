<template>
    <v-dialog
            v-model="alert.show"
            color="accent"
            dark
            max-width="400"
    >
        <v-card tile color="accent">
            <v-card-text>
                <validation-observer ref="observer" v-slot="{ passes }">
                    <v-form @submit.prevent="passes( submit )">
                        <v-row>
                            <v-col cols="12">
                                {{ alert.message }}
                            </v-col>
                            <v-col cols="12">
                                <validation-provider name="Скрэтч-код" rules="required" v-slot="{ errors }">
                                    <v-text-field
                                            placeholder="Введите скрэтч-код"
                                            v-model="form.scratchCode" type="text" :error-messages="errors"></v-text-field>
                                </validation-provider>
                            </v-col>
                            <v-col class="d-flex justify-end" cols="12">
                                <v-btn
                                        color="secondary"
                                        dark
                                        text
                                        type="submit"
                                >
                                    <small>Подтвердить</small>
                                </v-btn>
                                <v-btn
                                        type="button"
                                        dark
                                        text
                                        @click="closeAlert()"
                                ><small>Отменить</small>
                                </v-btn>
                            </v-col>
                        </v-row>
                    </v-form>
                </validation-observer>
            </v-card-text>

        </v-card>
    </v-dialog>
</template>

<script>
    export default {
        name: "ConfirmApproveAlert",
        data() {
            return {
                alert: {
                    show: false,
                    message: ''
                },
                form: {
                    user_id: null,
                    scratchCode: null
                }
            }
        },
        mounted() {
            this.$root.$on('confirm-approve', (data, message) => {
                this.form.user_id = data.user_id;
                this.alert.message = message;
                this.alert.show = true;
            });
            this.$root.$on('close-confirm', () => {
                this.closeAlert();
            })
        },
        methods: {
            submit() {
                this.$root.$emit('confirmed', '#3f51b5');
                this.alert.show = false;
                this.$httpService.post('api/v2/auth/user/approve', this.$fdService.fill(this.form))
                    .then(response => {
                        this.$root.$emit('successfully-approved');
                        this.$store.commit('alert', {status: true, message: response.data.success, type: 'success'});
                        this.$root.$emit('reload-users');
                    })
                    .finally(() => {
                        this.$root.$emit('confirmed', null);
                    })
            },
            closeAlert() {
                this.alert = {
                    show: false,
                    message: ''
                };
                this.form = {};
            }
        }
    }
</script>

<style scoped>

</style>
