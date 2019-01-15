<template>
    <div class="home">
        <Viewer />
    </div>
</template>

<script lang="ts">
    import {Component, Vue} from "vue-property-decorator";
    import Viewer from "@/components/Viewer/index.vue";

    @Component({
        components: {
            Viewer,
        },
    })
    export default class Home extends Vue {
    }
</script>
