<template>
    <div class="row">
        <!--site main area -->
        <div
            class="content-area shop-grid-content full-width col-lg-12 col-md-12 col-sm-12 col-xs-12"
        >
            <div class="site-main">
                <!-- Slide Banner -->
                <!-- <SlideBanner /> -->
                <Banner />

                <!--shop title-->
                <nuxt-link to="/products/brightening" tag="h3" class="custom_blog_title">
                    <a href="#">#Brightening</a>
                </nuxt-link>

                <!--product lists-->
                <ul class="row list-products auto-clear equal-container product-grid">
                    <template v-if="brightenings">
                        <Product
                            v-for="brightening in brightenings"
                            :key="brightening.id"
                            :product="brightening"
                        />
                    </template>
                </ul>

                <!--shop title-->
                <nuxt-link to="/products/purify" tag="h3" class="custom_blog_title">
                    <a href="#">#Purify</a>
                </nuxt-link>

                <!--product lists-->
                <ul class="row list-products auto-clear equal-container product-grid">
                    <template v-if="purifyings">
                        <Product
                            v-for="purifying in purifyings"
                            :key="purifying.id"
                            :product="purifying"
                        />
                    </template>
                </ul>

                <!--shop title-->
                <nuxt-link to="/products/decorative" tag="h3" class="custom_blog_title">
                    <a href="#">#Decorative</a>
                </nuxt-link>

                <!--product lists-->
                <ul class="row list-products auto-clear equal-container product-grid">
                    <template v-if="decoratives">
                        <Product
                            v-for="decorative in decoratives"
                            :key="decorative.id"
                            :product="decorative"
                        />
                    </template>
                </ul>

                <!--shop title-->
                <nuxt-link to="/products/extra care" tag="h3" class="custom_blog_title">
                    <a href="#">#ExtraCare</a>
                </nuxt-link>

                <!--product lists-->
                <ul class="row list-products auto-clear equal-container product-grid">
                    <template v-if="extracares">
                        <Product
                            v-for="extracare in extracares"
                            :key="extracare.id"
                            :product="extracare"
                        />
                    </template>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    layout: 'products',
    // middleware: 'check-previous-route',
    components: {
        // SlideBanner: () => import('~/components/SlideBanner'),
        Product: () => import('~/components/Product'),
        Banner: () => import('~/components/Banner')
    },
    data() {
        return {
            dataLoaded: false,
            products: [],
            brightenings: [],
            purifyings: [],
            decoratives: [],
            extracares: []
        }
    },
    head() {
        return {
            bodyAttrs: {
                class: this.dataLoaded
                    ? 'home-page home page page-id-4 page-template page-template-template-homepage page-template-template-homepage-php'
                    : ''
            }
        }
    },
    methods: {
        getProducts: function(category) {
            return this.$axios.get(`/products/${category}/0/4`)
        }
    },
    created() {
        this.getProducts('BRIGHTENING').then((response) => {
            if (response.data.data != 0) {
                this.brightenings = response.data.data
            }
        })

        this.getProducts('PURIFY').then((response) => {
            if (response.data.data != 0) {
                this.purifyings = response.data.data
            }
        })

        this.getProducts('DECORATIVE').then((response) => {
            if (response.data.data != 0) {
                this.decoratives = response.data.data
            }
        })

        this.getProducts('EXTRA CARE').then((response) => {
            if (response.data.data != 0) {
                this.extracares = response.data.data
            }
        })

        /* this.$axios.get(process.env.API_BASE_URL + 'products/product-codes').then((res) => {
            let urls = res.data.data.map((product) => {
                return `products/${product.kode_barang}/detail`
            })
            console.log(urls)
        }) */
    }
}
</script>
