<template>
    <div class="person">
        <div class="person__photo">
            <img :src="person.picture" alt="photo" />
        </div>
        <div class="person__info">
            <div class="person__info-header">
                <h4 class="person__info-name">{{ person.name }} ({{ person.age }})</h4>
                <div
                    v-if="person.group"
                    class="person__info-group"
                    :style="`--group-color: ${person.group.color}`"
                >
                    {{ person.group.text }}
                </div>
            </div>
            <div class="person__info-body">
                <div class="person__info-item">
                    <span class="bold">Почта:</span> {{ person.email }}
                </div>
                <div class="person__info-item">
                    <span class="bold">Дата регистрации:</span><br />{{ formatedDate }}
                </div>
                <div class="person__info-item">
                    <span class="bold">О себе:</span> {{ person.about }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'PersonCard',
    props: {
        person: {
            type: Object,
            default: null,
        },
    },
    computed: {
        formatedDate() {
            return new Intl.DateTimeFormat().format(new Date(this.person.registered));
        },
    },
};
</script>

<style scoped>
.person {
    display: grid;
    grid-template-columns: 50px 1fr;
    grid-gap: 10px;
}

.person__photo img {
    height: 50px;
    width: 50px;
    border-radius: 50%;
}

.person__info {
}

.person__info-name {
    margin: 0;
    font-size: 1.2em;
}

.person__info-header {
}

.person__info-body {
    margin-top: 20px;
}

.person__info-group {
    display: flex;
    align-items: center;
    margin-top: 7px;
}

.person__info-group::before {
    content: '';
    display: block;
    width: 14px;
    height: 14px;
    margin-right: 7px;
    position: relative;
    top: -1px;
    background-color: var(--group-color);
    border-radius: 2px;
}
.person__info-item + .person__info-item {
    margin-top: 10px;
}
.person__info-item .bold {
    font-weight: 700;
}
</style>
