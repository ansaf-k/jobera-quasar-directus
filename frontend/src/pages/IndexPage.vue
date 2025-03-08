<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import { directus } from 'src/boot/directus';
import { readItems } from '@directus/sdk';

interface Job {
    id: string;
    title: string;
    Company: string;
    Category: string;
    description: string;
}

const jobs = ref<Job[]>([]);
const selectedCategory = ref('');
const searchQuery = ref('');

async function fetchJobs() {
    try {
        const response = await directus.request<Job[]>(
            readItems('jobs', {
                filter: {
                    ...(selectedCategory.value && { Category: { _eq: selectedCategory.value } }), // Match the field name
                    ...(searchQuery.value && {
                        _or: [
                            { title: { _icontains: searchQuery.value } },
                            { Company: { _icontains: searchQuery.value } }, // Match the field name
                        ],
                    }),
                },
            })
        );
        jobs.value = response;
        console.log(jobs.value);
    } catch (error) {
        console.error('Error fetching jobs:', error);
    }
}

watch([selectedCategory, searchQuery], async () => {
    await fetchJobs();
});
onMounted(async () => {
    await fetchJobs();
});

</script>

<template>
    <div class="flex justify-between">
        <div style="min-width: 600px">
            <q-input class="q-ma-md" outlined v-model="searchQuery" label="Search jobs" />
        </div>

        <div class="q-ma-md">
            <select v-model="selectedCategory" class="q-pa-sm q-border q-rounded">
                <option value="">All Categories</option>
                <option value="Frontend">Frontend</option>
                <option value="Backend">Backend</option>
                <option value="Full Stack">Full Stack</option>
                <option value="UI/UX">UI/UX</option>
            </select>
        </div>
    </div>

    <div v-if="jobs.length === 0" class="text-center q-my-lg">
        <q-spinner color="primary" size="3em" />
        <p>Loading jobs...</p>
    </div>

    <div v-else>
        <div class="row q-col-gutter-md">
            <div v-for="job in jobs" :key="job.id" class="col-12 col-md-6 col-lg-4">
                <h2 class="dark text-xl font-semibold">{{ job.title }}</h2>
                <p class="dark mt-2">{{ job.Company }}</p>
                <p class="mt-2">{{ job.Category }}</p>
                <NuxtLink :to="`/jobs/${job.id}`" class="text-blue-500 hover:underline">
                    View Details
                </NuxtLink>
            </div>
        </div>
    </div>
</template>