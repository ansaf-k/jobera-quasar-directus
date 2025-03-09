<script setup lang="ts">
import MainHeader from 'src/components/MainHeader.vue';

</script>

<template>
    <q-layout view="hHh lpR fFf">

        <q-header elevated class="bg-primary text-white">
            <q-toolbar>
                <MainHeader />
            </q-toolbar>
        </q-header>

        <q-page-container>
            <router-view />
        </q-page-container>

    </q-layout>
</template>