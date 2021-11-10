<template>
    <div id="app">
        <div class="office">
            <template v-if="!isLoading">
                <Map :tables="tables" @open-profile="openProfile" @close-profile="closeProfile" />
                <SideMenu
                    :person="person"
                    :legends="legends"
                    :isUserOpenned.sync="isUserOpenned"
                    @close-profile="closeProfile"
                />
            </template>
            <div class="preloader" v-else>Loading...</div>
        </div>
    </div>
</template>

<script>
import Map from './components/Map.vue';
import SideMenu from './components/SideMenu.vue';

export default {
    name: 'App',
    data: () => ({
        people: [],
        legends: [],
        tables: [],
        person: null,
        isLoading: false,
        isUserOpenned: false,
    }),
    components: {
        Map,
        SideMenu,
    },
    created() {
        this.loadData();
    },
    methods: {
        async loadData() {
            try {
                this.isLoading = true;

                const tables = (await import('@/assets/data/tables.json')).default;
                const legends = (await import('@/assets/data/legend.json')).default;
                const people = (await import('@/assets/data/people.json')).default;

                this.people = people;
                this.legends = legends.map((legend) => {
                    return {
                        ...legend,
                        counter: tables.reduce(
                            (count, table) =>
                                table.group_id === legend.group_id ? count + 1 : count,
                            0
                        ),
                    };
                });
                this.tables = tables.map((table) => {
                    return {
                        ...table,
                        legend: legends.find((it) => it.group_id === table.group_id),
                    };
                });
            } catch (error) {
                console.error(error);
            } finally {
                this.isLoading = false;
            }
        },
        getPerson(tableId) {
            const person = this.people.find((person) => person.tableId === tableId);
            const table = this.tables.find((table) => table._id === tableId);

            return person ? { ...person, group: table.legend } : null;
        },
        openProfile(tableId) {
            this.person = this.getPerson(tableId);
            this.isUserOpenned = true;
        },
        closeProfile() {
            this.person = null;
            this.isUserOpenned = false;
        },
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    color: #2c3e50;
    background-color: #fafafa;
    padding: 24px;
    box-sizing: border-box;
}

html,
body,
#app {
    height: 100%;
}

* {
    box-sizing: border-box;
}

h3 {
    margin-top: 0px;
}

.office {
    display: grid;
    grid-template-columns: 1fr 320px;
    border-radius: 6px;
    border: 1px solid #ccd8e4;
    height: 100%;
    background: white;
    max-width: 1500px;
    margin: 0 auto;
}

.preloader {
    padding: 24px;
}
</style>
