<script setup lang="ts">
import { ref } from 'vue';
import Textarea from 'primevue/textarea';
import Button from 'primevue/button';
import InputGroup from 'primevue/inputgroup';
import Card from 'primevue/card';
import { useRouter } from 'vue-router';

const query = ref(''); // To hold the value of the textarea
const router = useRouter(); // Vue router instance to programmatically navigate

// Function that handles the form submission
const submitQuery = async () => {
  if (!query.value.trim()) {
    // If the query is empty or only whitespace, do not proceed
    return;
  }

  // You would need to make an API call to your back-end service here
  // For the purpose of demonstration, we will use the query directly in the URL

  // Encode the query to be URL-safe
  const encodedQuery = encodeURIComponent(query.value.trim());

  // Redirect to the new page with the query as a parameter
  // Replace '/results-page' with the path to your actual results page
  router.push({ name: 'response', params: { topic: encodedQuery } });
};
</script>

<template>
  <Card :style="{ maxWidth: '900px',position:'fixed',top:'50%',left:'50%',transform:'translate(-50%,-50%)' }">
    <template #title>You wanna LearnWhy they teach you that?</template>
    <template #content>
      <p>Uncover the genuine essence of intricate scientific subjects through compelling narratives.</p>

      <!-- InputGroup Component -->
      <InputGroup>
        <Textarea v-model="query" placeholder="Type your question here..." rows="5" autoResize />
        <Button @click="submitQuery" icon="pi pi-search" class="p-button-primary" />
      </InputGroup>
    </template>
  </Card>
</template>

<style>
/* Add any additional styles here */
</style>