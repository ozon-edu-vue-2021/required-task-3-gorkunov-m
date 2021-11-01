<template>
    <div class="menu">
        <div class="toolbar">
            <div class="toolbar__header">
                <template v-if="!isUserOpenned">
                    <h3>Информация</h3>
                </template>
                <template v-else>
                    <button class="action" @click="closeProfile">
                        <div class="arrow"></div>
                    </button>
                    <h3>Профиль</h3>
                </template>
            </div>
        </div>
        <div class="content">
            <div v-if="!isUserOpenned" class="legend">
                <div class="legend__data">
                    <Draggable v-model="legends" v-if="legends.length > 0" class="legend__items">
                        <legend-item
                            v-for="item in legends"
                            :key="item.group_id"
                            :color="item.color"
                            :text="item.text"
                            :counter="item.counter"
                            class="legend__item"
                        />
                    </Draggable>
                    <span v-else class="legend--empty">Список пуст</span>
                </div>
                <div class="legend__chart" v-if="chart.data">
                    <PieChart :data="chart.data" :options="chart.options" />
                </div>
            </div>
            <div v-else class="profile">
                <div v-if="!person" class="profile__empty">Место пустое</div>
                <PersonCard v-else :person="person" />
            </div>
        </div>
    </div>
</template>

<script>
import LegendItem from './SideMenu/LegendItem.vue';
import PersonCard from './SideMenu/PersonCard.vue';
import PieChart from './SideMenu/PieChart.vue';
import Draggable from 'vuedraggable';

export default {
    name: 'SideMenu',
    props: {
        isUserOpenned: {
            type: Boolean,
            default: false,
        },
        person: {
            type: Object,
            default: null,
        },
        legends: {
            type: Array,
            default: () => [],
        },
    },
    data: () => ({
        chart: {
            data: null,
            options: {
                borderWidth: '10px',
                legend: {
                    display: false,
                },
                responsive: true,
            },
        },
    }),
    components: {
        LegendItem,
        PersonCard,
        Draggable,
        PieChart,
    },
    methods: {
        closeProfile() {
            this.$emit('update:isUserOpenned', false);
        },
    },
    mounted() {
        this.chart.data = this.legends.reduce(
            (data, legend) => {
                const dataset = data.datasets[0];
                const datasetOptions = {
                    label: 'Легенда',
                    backgroundColor: [...dataset.backgroundColor, legend.color],
                    data: [...dataset.data, legend.counter],
                };

                return {
                    labels: [...data.labels, legend.text],
                    datasets: [datasetOptions],
                };
            },
            {
                labels: [],
                datasets: [{ backgroundColor: [], data: [] }],
            }
        );
    },
};
</script>

<style scoped>
.menu {
    border-left: 1px solid #ccd8e4;
    padding: 24px;
    display: flex;
    flex-direction: column;
    overflow: hidden;
}

.toolbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.toolbar__header {
    display: flex;
    align-items: center;
}

.toolbar__header .action {
    cursor: pointer;
    margin-right: 14px;
    width: 20px;
    height: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    border: none;
    padding: 0;
    background-color: transparent;
}

.toolbar__header .action .arrow {
    width: 10px;
    height: 10px;
    border-top: 2px solid blue;
    border-right: 2px solid blue;
    transform: rotate(-135deg);
}

h3 {
    margin: 0;
}

.content {
    flex: 1;
}

.toolbar + .content {
    margin-top: 25px;
}

.content .legend {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 100%;
}

.content .legend .legend__data {
    display: flex;
    height: 100%;
}

.content .legend .legend__items {
    flex: 1;
    width: 100%;
}

.content .legend .legend__items .legend__item:not(:first-child) {
    margin-top: 16px;
}

.content .legend .legend__items .legend__item {
    cursor: pointer;
}

.content .legend .legend__items .legend__item.sortable-chosen {
    opacity: 25%;
}

.content .legend .legend--empty {
    align-self: center;
    width: 100%;
    text-align: center;
}

.profile {
}
</style>
