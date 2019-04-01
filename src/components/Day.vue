<template>
    <div
        class="dayDisplay"
        v-if="data.day !== undefined" 
        v-on:mouseover="becameActive"
        v-on:click="becameClicked"
    >
        {{ data.day }}<br>
        <b-badge
            v-if="data.count > 0" 
            pill 
            variant="danger"
        > 
            {{ data.count }}
        </b-badge>
        <span v-else-if="!this.DISPLAY_ZEROS">&nbsp;</span>
        <b-badge
            v-if="DISPLAY_ZEROS && data.count == 0" 
            pill 
            variant="success"
        >0</b-badge>
    </div>
    <div v-else style="height: 55px;"></div>
</template>


<script>
export default {
    name: "Day",
    props: ['data'],
    data(){
        return {
            DISPLAY_ZEROS: false
        }
    },
    methods: {
        becameActive(){
            this.$emit('became-active', {year: this.data.year, month: this.data.month})
        },
        becameClicked(){
            this.$emit('became-clicked', {year: this.data.year, month: this.data.month, day: this.data.day});
        }
    }
}
</script>

<style scoped>
 .dayDisplay{
     line-height: 20px;
     margin: 3px 0px;
 }
</style>
