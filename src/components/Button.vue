<template lang="en">
    <button v-if="variant === 'primary'"  class="bg-stone-950 text-white uppercase px-4 py-2 rounded-sm hover:bg-stone-700" :class="class">
        <slot />
    </button>

    <button v-if="variant === 'link'" class="uppercase px-4 py-2 hover:text-yellow-500 font-semibold text-sm" :class="class">
       <slot />
    </button>
</template>

<script>
export default {
    name: "Button",
    props: {
        variant: String,
        class: String
    }
}
</script>
