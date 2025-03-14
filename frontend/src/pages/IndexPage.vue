<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import { directus } from 'src/boot/directus';
import { readItems } from '@directus/sdk';
import type { Job } from 'src/boot/JobModel';



const jobs = ref<Job[]>([]);
const selectedCategory = ref('');
const searchQuery = ref('');

async function fetchJobs() {
    try {
        const response = await directus.request<Job[]>(
            readItems('jobs', {
                filter: {
                    ...(selectedCategory.value && { Category: selectedCategory.value.toLowerCase() }),
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
console.log(window);
onMounted(async () => {
    await fetchJobs();
    // console.log(window);
});

</script>

<template>
    <div class="q-pa-md">
        <!-- Search and Filter Bar -->
        <div class="row items-center q-mb-lg q-gutter-md justify-between">
            <div class="col-12 col-md-7 q-pr-md-md">
                <q-input outlined v-model="searchQuery" label="Search jobs" class="full-width" dense bg-color="white"
                    clearable>
                    <template v-slot:prepend>
                        <q-icon name="search" />
                    </template>
                </q-input>
            </div>

            <div class="col-12 col-md-4 q-mt-md-none">
                <q-select outlined v-model="selectedCategory" :options="[
                    { label: 'All Categories', value: '' },
                    { label: 'Frontend', value: 'Frontend' },
                    { label: 'Backend', value: 'Backend' },
                    { label: 'Full Stack', value: 'Full Stack' },
                    { label: 'UI/UX', value: 'UI/UX' }
                ]" label="Filter by category" dense bg-color="white" class="full-width" />
            </div>
        </div>

        <div v-if="jobs.length === 0" class="text-center q-py-xl">
            <q-icon name="work_off" size="4rem" color="grey-5" />
            <p class="text-h6 text-grey-8 q-mt-md">No Jobs Found</p>
        </div>

        <div v-else class="row q-col-gutter-md">
            <div v-for="job in jobs" :key="job.id" class="col-12 col-md-6 col-lg-4">
                <q-card flat bordered class="job-card full-height">
                    <q-card-section>
                        <div class="row items-center no-wrap">
                            <div class="col">
                                <div class="text-h6 text-weight-bold ellipsis">{{ job.title }}</div>
                                <div class="text-subtitle1 text-primary text-weight-medium q-mt-xs">{{ job.Company }}
                                </div>
                            </div>
                            <q-chip outline color="primary" text-color="primary" size="sm" class="q-ml-sm">
                                {{ job.Category }}
                            </q-chip>
                        </div>
                    </q-card-section>

                    <q-separator />

                    <q-card-actions>
                        <q-btn :to="{ path: `/job/${job.id}` }" flat color="primary" label="View Details"
                            icon="arrow_forward" />
                    </q-card-actions>
                </q-card>
            </div>
        </div>
    </div>
</template>