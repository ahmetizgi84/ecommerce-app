<template lang="en">
    <div class="h-32">
        <div class="max-w-7xl bg-white m-auto h-full flex items-center px-4">
            <p class="text-lg font-bold">
                E-commerce App
            </p>

            
            <div class="ml-auto flex items-center">
                <Button variant="primary" class="md:hidden">m</Button>
                <ul class="hidden items-center gap-6 md:flex">
                    <li><Button variant="link">catalog</Button></li>
                    <li><Button variant="link">fashion</Button></li>
                    <li><Button variant="link">favourite</Button></li>
                    <li><Button variant="link">lifestyle</Button></li>
                    <li><Button variant="primary">sign up</Button></li>
                </ul>
            </div>
        </div>
    </div>
</template>


<script>
import Button from './Button.vue'
export default {
    name: "Header",
    components: {
        Button
    }
}
</script>
