<template>
    <div class="map" @click="clickHandler">
        <h3>Карта офиса</h3>
        <div class="map-root">
            <MapSvg ref="map" />
        </div>
        <WorkPlaceSvg ref="place" v-show="false" />
    </div>
</template>

<script>
import * as d3 from 'd3';
import MapSvg from '@/assets/images/map.svg';
import WorkPlaceSvg from '@/assets/images/workPlace.svg';

export default {
    name: 'Map',
    props: {
        tables: {
            type: Array,
            default: () => [],
        },
    },
    components: {
        MapSvg,
        WorkPlaceSvg,
    },
    data: () => ({
        mapEl: null,
        tableEl: null,
    }),
    async mounted() {
        this.mapEl = d3.select(this.$refs.map);
        this.tableEl = d3.select(this.$refs.place).select('.table');
        this.drawTables();
    },
    methods: {
        clickHandler(e) {
            const place = e.target.closest('.place');

            if (place) {
                this.$emit('open-profile', Number(place.dataset.id));
            } else {
                this.$emit('close-profile');
            }
        },
        drawTables() {
            const placesEl = this.mapEl.append('g').classed('places', true);

            this.tables.forEach((table) => {
                const place = placesEl
                    .append('g')
                    .attr('transform', `translate(${table.x}, ${table.y}) scale(0.5)`)
                    .attr('data-id', table._id)
                    .classed('place', true);

                place
                    .append('g')
                    .attr('transform', `rotate(${table.rotate || 0})`)
                    .attr('group_id', table.group_id)
                    .classed('table', true)
                    .html(this.tableEl.html())
                    .attr('fill', table.legend?.color ?? 'transparent');
            });
        },
    },
};
</script>

<style scoped>
.map {
    height: 100%;
    width: 100%;
    padding: 24px;
    overflow: hidden;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
}

.map-root {
    height: 100%;
    width: 100%;
    overflow: hidden;
    box-sizing: border-box;
}

h3 {
    margin-top: 0px;
}

::v-deep svg {
    height: 100%;
    width: 100%;
}

::v-deep .table {
    cursor: pointer;
}
</style>
