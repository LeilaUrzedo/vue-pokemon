<template>
    <main>
        <PokemonCard 
            v-bind:name="this.result.name"
            v-bind:img="this.result.img" 
            v-bind:height="this.result.height"
            v-bind:weight="this.result.weight"
            v-bind:abilities="this.result.abilities"
            v-bind:types="this.result.types"
        ></PokemonCard>
        <div class="control">
            <form class="form-search" v-on:submit.prevent="searchPokemon">
                <label for="search">Encontre um Pokémon:</label>
                <input id="search" v-model="term" @keyup.native.enter="searchPokemon" @input="updateSuggestionsList" />
                <button type="submit">Buscar</button>
            </form> 
            <div class="nav-control">
                <label for="qts-pokemon">Quantidades por página</label>
                <select id="qts-pokemon" class="nav-select" v-model="option" @change="populateSuggestionsList">
                    <option value="10">10</option>
                    <option value="20">20</option>
                    <option value="30">30</option> 
                </select>
                <p class="nav-pagination">
                    <a class="prev" v-on:click.prevent="populateSuggestionsList('previous')" v-show="previous !== null">Anterior</a>
                    <a class="next" v-on:click.prevent="populateSuggestionsList('next')" v-show="next !== null">Próximo</a>
                </p>
            </div>
        </div>
        <ul class="list-porkemon">
            <li class="item-pokemon" v-for="pokemon in limitedFilteredPokemonList" :key="pokemon.id">
                <div class="pokemon" @click.prevent="transferSelectedSuggestion(pokemon.name)">
                    <figure>
                        <img class="thumb-pokemon" :src="imgUrl + pokemon.id + '.png'" width="96" height="96" alt="">
                    </figure>
                    <p>
                        <strong class="nome-pokemon">{{ pokemon.name }}</strong>
                        <span class="id-pokemon">{{ pokemon.id }}</span>
                    </p>
                </div>
            </li>
        </ul>
    </main>
</template>

<script>
import PokemonCard from "./PokemonCard";

export default {
    name: "Main",
    components: {
        PokemonCard
    },
    props: {
    },
    data() {
        return {
            term: "",
            imgUrl: 'https://pokeres.bastionbot.org/images/pokemon/',
            pokemonList: [],
            filteredPokemonList: [],
            option: 10,
            result: {
                name: String,
                img: String,
                height: Number,
                weight: Number,
                abilities: [],
                types: []
            }
        }
    },
    computed: {
        getTerm() {
            return this.term;
        },
        limitedFilteredPokemonList() {
            return this.filteredPokemonList;
        }
    },
    methods: {
        populateSuggestionsList(action) {
            this.pokemonList = [];
            var url = `https://pokeapi.co/api/v2/pokemon?limit=` + this.option;
            if(action === 'previous') {
                url = this.previous;
            } else if (action === 'next') {
                url = this.next;
            }
            fetch(url)
            .then(res => {
                res.json().then(data => {
                    data.results.map(result =>
                        this.pokemonList.push({
                            name: result.name,
                            img: result.img,
                            id: result.url.split("/").reverse()[1]
                        })
                    );    
                    this.filteredPokemonList = this.pokemonList;
                    this.previous = data.previous;
                    this.next = data.next;
                });
            })
            .catch(err => console.error(err));
        },
        updateSuggestionsList() {
            /* eslint-disable no-unused-vars */
            const self = this;
            this.filteredPokemonList = this.pokemonList.filter(item =>
                item.name.includes(this.getTerm.toLowerCase())
            );
        },
        transferSelectedSuggestion(term) {
            const self = this;
            this.term = term;
            this.searchPokemon();
        },
        searchPokemon() {
            const self = this;
            if (!this.getTerm) {
                return;
            }
            /* eslint-enable no-unused-vars */
            //fetch api
            fetch(`https://pokeapi.co/api/v2/pokemon/${(this.getTerm).toLowerCase()}`)
                .then(res => {
                    res.json().then(data => {
                        this.result.name = data.name;
                        this.result.img = data.sprites.front_default;
                        this.result.height = data.height;
                        this.result.weight = data.weight;
                        this.result.abilities = data.abilities;
                        this.result.types = data.types;
                    });
                })
            .catch(err => console.error(err));
        }
    },
    mounted() { 
        this.populateSuggestionsList();
        this.searchPokemon();
    }
}
</script>

<style lang="scss" scoped>

.list-porkemon {
    display: grid;
    grid-gap: 1rem;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    margin: 2rem auto;
    padding: 0;
    li {
        flex: 0 0 120px;
        list-style: none;
        border: 1px solid rgba(0,0,0,.2);
        border-radius: 20px;
        box-shadow: 0 5px 15px rgba(0,0,0,.2);
        cursor: pointer;
        transition: 0.4s;
        margin: 0 1px .75em;
        padding: 1rem;
        min-height: 4rem;
        &:hover {
            transform: scale(0.9, 0.9);
        }
    }
}

.control {
    display: flex;
    justify-content: space-between;
    margin: 2rem 0;
    .nav-control {
        text-align: right;
        .nav-select {
            display: inline-block;
            max-width: 70px;
            margin:0 0 10px 10px;
        }
    }
}

.nav-pagination {
    a {
        cursor: pointer;
        border: solid black;
        border-width: 0 3px 3px 0;
        display: inline-block;
        font-size: 0;
        padding: 6px;
        &.next {
            transform: rotate(-45deg);
            -webkit-transform: rotate(-45deg);
        }

        &.prev {
            transform: rotate(135deg);
            -webkit-transform: rotate(135deg);
        }
    }
}

.pokemon {
    display: flex;
    align-items: center; 
    .nome-pokemon {
        display: block;
        font-weight: normal;
        &::before{
            content: "Nome: ";
        }
    }
    .id-pokemon {
        display: block;
        &::before{
            content: "Id: ";
        }
    }
}

@media (min-width: 600px) {
  .list-porkemon { grid-template-columns: repeat(2, 1fr); }
}

@media (min-width: 900px) {
  .list-porkemon { grid-template-columns: repeat(3, 1fr); }
}
</style>