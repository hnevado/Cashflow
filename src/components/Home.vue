<template>

    <Layout>
        <template #header>
            <Header/>
        </template>
        <template #resume>
            <Resume
             :label="label"
             :totalLabel="'Ahorro total'"
             :amount="amount"
             :totalAmount="totalAmount">
                <template #graphic v-if="movements.length > 2">
                    <Graphic :amounts="amounts" @select="select"/>
                </template>
                <template #action>
                    <Action @create="create"/> 
                </template>
             </Resume>
        </template>
        <template #history>
            <History
                :movements="movements"
                @remove="remove"
            />
        </template>
    </Layout>
    
</template>

<script>
import Header from "./Header.vue";
import Layout from "./Layout.vue";
import Resume from "./Resume.vue";
import History from "./History.vue";
import Action from "./Action.vue";
import Graphic from "./Graphic.vue";
export default {
    data() {
        return {
            amount: null,
            label: null,
            movements: [],
        }
    },
    computed: {
        amounts() {
            const lastDays = this.movements.filter(m => {
                console.log("m");
                console.log(m.amount);
                const today = new Date();
                const oldDate = today.setDate(today.getDate() - 30);

                return m.time >= oldDate;

            }).map(m => m.amount);
            console.log("Last Days");
            console.log(lastDays);
            return lastDays.map((m,i) => {

                const lastMovements = lastDays.slice(0,i +1);
                
                return lastMovements.reduce((suma, movement) => {
                    console.log("Movement es "+movement);
                    return suma + movement; 
                }, 0);

            });
        },
        totalAmount() {
            return this.movements.reduce((suma,m) => {

                return suma+m.amount;

            }, 0);
        }
    },
    mounted() {
        const movements = JSON.parse(localStorage.getItem("movements"));

        if (Array.isArray(movements)) {
            this.movements = movements.map(m => {

                return {...m, time: new Date(m.time)};

            });
      }
    },
    methods: {

        create(movement) {


            this.movements.push(movement);
            this.save();
        },
        remove(id)
        {
            const index = this.movements.findIndex(m => m.id === id);
            this.movements.splice(index, 1);
            this.save();
        },
        save() {
            localStorage.setItem("movements", JSON.stringify(this.movements));
        },
        select(el) {
            this.amount = el;
        }


    },
    components: {
        Layout,
        Header,
        Resume,
        History,
        Action,
        Graphic
    }

}

</script>
