<template>
    <div>
        <Suspense>
            <AsyncCity />
            <template #fallback>
                <p>Loading...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCity from '../components/AsyncCityView.vue';

</script>