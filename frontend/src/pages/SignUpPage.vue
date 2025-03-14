<script setup lang="ts">
import { ref } from 'vue';

const email = ref('');
const username = ref('');
const password = ref('');
const isPwd = ref(true);

const onSubmit = () => {
    // Here you would typically handle the login logic with your backend
    console.log('SignUp submitted:', { email: email.value, password: password.value, username: username.value });
};



const onLogin = () => {
    // Navigate to signup page
    console.log('Navigate to signup page');
};

</script>

<template>
    <q-layout view="lHh Lpr lFf">
        <q-page-container>
            <q-page class="flex flex-center bg-grey-1">
                <q-card class="login-card q-pa-md">
                    <q-card-section class="text-center">
                        <div class="text-h5 q-mb-md">Sign Up</div>
                    </q-card-section>

                    <q-card-section>
                        <q-form @submit="onSubmit" class="q-gutter-md">
                            <q-input v-model="email" label="Email" type="email" filled lazy-rules :rules="[
                                val => !!val || 'Email is required',
                            ]">
                                <template v-slot:prepend>
                                    <q-icon name="email" />
                                </template>
                            </q-input>

                            <q-input v-model="username" label="Username" type="text" filled lazy-rules :rules="[
                                val => !!val || 'Username is required',
                            ]">
                                <template v-slot:prepend>
                                    <q-icon name="person" />
                                </template>
                            </q-input>

                            <q-input v-model="password" label="Password" :type="isPwd ? 'password' : 'text'" filled
                                lazy-rules :rules="[
                                    val => !!val || 'Password is required',
                                ]">
                                <template v-slot:prepend>
                                    <q-icon name="lock" />
                                </template>
                                <template v-slot:append>
                                    <q-icon :name="isPwd ? 'visibility_off' : 'visibility'" class="cursor-pointer"
                                        @click="isPwd = !isPwd" />
                                </template>
                            </q-input>

                            <q-btn type="submit" color="primary" label="Sign Up" no-caps class="full-width q-py-sm" />
                        </q-form>
                    </q-card-section>


                    <q-card-section class="text-center q-pt-none">
                        <div class="row justify-center items-center">
                            <div class="text-grey-6">already have an account?</div>
                            <q-btn flat color="primary" label="Login" no-caps @click="onLogin" />
                        </div>
                    </q-card-section>
                </q-card>
            </q-page>
        </q-page-container>
    </q-layout>
</template>


<style scoped>
.login-card {
    width: 100%;
    max-width: 400px;
    border-radius: 10px;
}
</style>