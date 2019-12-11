<template>
    <tr class="cart_item">
        <td class="product-thumbnail">
            <a href="#">
                <img
                    :src="`${$axios.defaults.baseURL}assets/img/thumbnails/${item.picture}.jpg`"
                    :alt="item.product_name"
                    class="attachment-shop_thumbnail size-shop_thumbnail wp-post-image"
                />
            </a>
        </td>
        <td class="product-name" data-title="Product">
            <nuxt-link
                :to="`/products/${item.product_code}/detail`"
                tag="a"
                class="title"
            >{{ item.product_name }}</nuxt-link>
            <span class="attributes-select attributes-color">{{ item.price | rupiah }}</span>
            <!-- <span class="attributes-select attributes-size">, XS</span> -->
        </td>
        <td class="product-quantity" data-title="Quantity">
            <div class="quantity">
                <div class="control">
                    <span class="btn-number qtyminus quantity-minus" @click="minQty">-</span>
                    <input
                        type="text"
                        data-step="1"
                        min="1"
                        v-model="qty"
                        title="Qty"
                        class="input-qty qty"
                        size="4"
                        @input="updateQty"
                    />
                    <span class="btn-number qtyplus quantity-plus" @click="addQty">+</span>
                </div>
            </div>
        </td>
        <td class="product-price" data-title="Price">
            <!-- <span class="woocommerce-Price-amount">
                <span class="woocommerce-Price-currencySymbol">Disc</span>
                45%
            </span>-->
        </td>
        <td class="product-price" data-title="Price">
            <span class="woocommerce-Price-amount amount">
                <span class="woocommerce-Price-currencySymbol">
                    <sub>
                        <!-- <strike>Rp. 232312</strike> -->
                        <strike>
                            <br />
                        </strike>
                    </sub>
                </span>
                <p>{{ item.subtotal | rupiah }}</p>
            </span>
        </td>
        <td class="product-remove">
            <a class="remove" @click="removeItem"></a>
        </td>
    </tr>
</template>

<script>
export default {
    props: ['item'],
    data() {
        return {
            qty: this.item.qty
        }
    },
    methods: {
        updateQty: function() {
            this.$store.dispatch('cart/updateQty', {
                product_code: this.item.product_code,
                qty: this.qty
            })
        },
        addQty: function() {
            this.qty++
            this.updateQty()
        },
        minQty: function() {
            if (this.qty > 1) {
                this.qty--
                this.updateQty()
            }
        },
        addItem: function() {
            // this.item.qty = this.qty
            // this.$store.dispatch('cart/addItem', this.item)
            // this.qty = 1
        },
        removeItem: function() {
            this.$store.dispatch('cart/removeItem', this.item.product_code)
        }
    }
}
</script>

<style scoped>
.cart-form .shop_table .product-price {
    width: 18%;
    font-size: 20px;
    color: #111;
    font-weight: 700;
}

.cart-form .shop_table .product-quantity {
    width: 15%;
}

.cart-form .shop_table .product-name {
    font-weight: 600;
    color: #333333;
    width: 25%;
    font-size: 16px;
}
</style>