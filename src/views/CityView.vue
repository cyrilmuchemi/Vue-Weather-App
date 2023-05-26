<template>
    <div>
        <div>
          <Suspense>
            <AsyncCityView/>
            <template #fallback>
                
            </template>
          </Suspense>
        </div>
    </div>
</template>

<script setup>
import AsyncCityView from '@/components/AsyncCityView.vue';
</script>