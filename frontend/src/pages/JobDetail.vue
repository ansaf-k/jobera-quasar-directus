<script setup lang="ts">
import { readItems } from '@directus/sdk';
import { directus } from 'src/boot/directus';
import type { Job } from 'src/boot/JobModel';
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const job = ref<Job | null>(null);
const loading = ref(true);

onMounted(async () => {
  try {
    const response = await directus.request<Job[]>(
      readItems('jobs', {
        filter: { id: route.params.id },
      })
    );
    job.value = response.length > 0 ? (response[0] as Job) : null;
    console.log(job.value);
  } catch (error) {
    console.error('Error fetching jobs:', error);
  } finally {
    loading.value = false;
  }
});

const formatDate = (dateString: string | null) => {
  if (!dateString) return 'N/A';
  const date = new Date(dateString);
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  });
};


</script>

<template>
  <q-page class="q-pa-md">
    <!-- Back Button -->
    <q-btn to="/" color="primary" icon="arrow_back" label="Back to Jobs" class="q-mb-md" />

    <!-- Job Details Card -->
    <div v-if="job && !loading">
      <q-card class="job-detail-card q-pa-md">
        <q-card-section>
          <div class="text-h5 text-weight-bold q-mb-sm">{{ job.title }}</div>
          <q-chip color="primary" text-color="white" icon="work" class="q-mb-md">
            {{ job.Category }}
          </q-chip>

          <div class="text-subtitle1 text-weight-bold">Company</div>
          <div class="q-mb-md">{{ job.Company }}</div>

          <div class="text-subtitle1 text-weight-bold">Description</div>
          <div class="q-mb-md">{{ job.Description }}</div>

          <div class="text-subtitle1 text-weight-bold">Status</div>
          <q-badge color="grey" class="q-pa-xs">
            {{ job.status.toUpperCase() }}
          </q-badge>

          <div class="row q-mt-lg">
            <div class="col-12 col-sm-6 q-mb-md">
              <div class="text-subtitle2 text-weight-bold">Posted on</div>
              <div>{{ formatDate(job.date_created) }}</div>
            </div>

            <div class="col-12 col-sm-6 q-mb-md" v-if="job.date_updated">
              <div class="text-subtitle2 text-weight-bold">Last Updated</div>
              <div>{{ formatDate(job.date_updated) }}</div>
            </div>
          </div>
        </q-card-section>
      </q-card>
    </div>

    <!-- Loading State -->
    <div v-else-if="loading" class="flex flex-center column">
      <q-spinner color="primary" size="3em" />
      <p class="q-mt-sm">Loading job details...</p>
    </div>

    <!-- No Job Found State -->
    <div v-else class="text-center q-pa-lg">
      <q-icon name="error_outline" size="3em" color="negative" />
      <p class="text-h6 q-mt-md">Job not found</p>
      <q-btn to="/" color="primary" label="Back to Jobs List" class="q-mt-md" />
    </div>
  </q-page>
</template>

<style scoped>
.job-detail-card {
  max-width: 800px;
  margin: 0 auto;
}
</style>