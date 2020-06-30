<template>
    <div class="product-list">
        <button v-on:click=toggleDomain>{{this.displayLanguage(this.domain)}}</button>
        <div class="product-list-container">
            <div class="box" v-for="product in mergedProducts" :key="product.reference">
                <div class="card">
                    <div class="content">
                        <div class="front details">
                            <img v-bind:alt=product.imageAltText v-bind:src="'http://colorline.no' + product.image"><br>
                            <div class="product-details">
                                <div class="title"> {{ product.title }}</div>
                                {{ getDate(product.Dates) }} {{ getTime(product.Dates) }}<br><br>
                                <div class="short-description">{{ product.shortDescription }}</div>
                                <h3>{{ product.location }}</h3>
                                <div class="price">{{ product.Price.Amount }} {{ product.Price.Currency }}</div>
                            </div>
                        </div>
                        <div class="back long-description">
                            <div class="title"> {{ product.title }}</div>
                            <div v-html="product.longDescription"></div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import '../assets/css/main.css';

    export default {
        data() {
            return {
                mergedProducts: [],
                domain: "no"
            }
        },

        async created() {
            this.fetchProducts();
        },

        methods: {
            async fetchProducts() {
                let productsResponse = await fetch('jsonData.json').catch(e => console.log('Error: ', e.message));
                let products = await productsResponse.json();

                let enrichedProductsResponse = await fetch('https://www.colorline.no/api/rest/extras?ignoreValidation', {
                    method: 'POST',
                    body: JSON.stringify({
                        sailingsReference: "OOOYYY000000000000",
                        domain: this.domain,
                        getProducts: [
                            products.map(p => p.Reference)
                        ]
                    })
                }).catch(e => console.log('Error: ', e.message));

                let enrichedProducts = await enrichedProductsResponse.json();

                this.mergedProducts = this.mergeProductLists(products, enrichedProducts);
            },

            mergeProductLists(products, enrichedProducts) {
                enrichedProducts.products.reverse();
                return products.map((product, i) => Object.assign({}, product, enrichedProducts.products[i]));
            },

            getDate(dates) {
                if (dates.length) {
                    let date = new Date(dates[0].Date);
                    let formattedDate = `${date.getDate()}.${date.getMonth() + 1}.${date.getFullYear()}`;
                    return formattedDate;
                }
                return "";
            },

            getTime(dates) {
                if (dates.length) {
                    if (dates[0].Timeslots.length) {
                        return dates[0].Timeslots[0].Time;
                    }
                }
                return "";
            },

            toggleDomain() {
                this.domain = this.domain === "no" ? "com" : "no"
                this.fetchProducts();
            },

            displayLanguage(domain) {
                if (domain == "com") {
                    return "En"
                }
                return "No"
            }
        }
    }
</script>
