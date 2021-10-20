<template>
    <div>
        <b-row>
            Court {{number}}
        </b-row>
        <b-row>
            <b-col>
                <b-row>
                    <div class='drop-zone'>
                        <div v-for='item in side1' :key='item.title' class='drag-el'>
                            {{ item.title }}
                        </div>
                    </div>
                </b-row>
            </b-col>
            <b-col>
                <b-row>
                    <div class='drop-zone'>
                        <div v-for='item in side2' :key='item.title' class='drag-el'>
                            {{ item.title }}
                        </div>
                    </div>
                </b-row>
            </b-col>
        </b-row>
    </div>
</template>
<script>
export default{
    data(){
        return{
            side1: [],
            side2: []
        };
    },
    props: {
        number: {
            required:true
        },
        singles: {
            type: Boolean,
            required: true
        }

    }

}
</script>
<style scoped>
  .drop-zone {
    background-color: #eee;
    margin-bottom: 10px;
    padding: 10px;
  }

  .drag-el {
    background-color: #fff;
    margin-bottom: 10px;
    padding: 5px;
  }
</style>