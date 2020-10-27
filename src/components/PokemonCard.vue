<template>
    <div class="card" v-if="typeof name !== 'function'" >
        <h2 class="title-card">{{ name }}</h2>
        <div class="img-card">
            <figure>
                <img :src="img" />
            </figure>
        </div>
        <p><strong> Peso: </strong>{{ weight }}</p>
        <p><strong>Altura: </strong> {{ height }}</p>
        <p><strong>Habilidades: </strong> <span v-for="ability in abilities" :key="ability.id">{{ ability.ability.name }} </span> </p>
        <p><strong>Tipo: </strong> <span v-for="type in types" :key="type.id">{{ type.type.name }}</span></p>
    </div>
</template>

<script>
export default {
    name: "PokemonCard",
    props: {
        name: {},
        img: {},
        height: {},
        weight: {},
        abilities: {},
        types: {}
    }
};
</script>

<style lang="scss" scoped>
    .card {
        border: 1px solid rgba(0,0,0,.2);
        border-radius: 20px;
        box-shadow: 0 5px 15px rgba(0,0,0,.2);
        padding: 1rem;
        margin-bottom: 2rem;

        .title-card {
            font-size: 22px;
        }
    }
</style>