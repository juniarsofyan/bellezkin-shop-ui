<template>
    <ul class="row list-products auto-clear equal-container product-list">
        <li
            class="product-item style-list col-lg-12 col-lg-12 col-md-12 col-sm-12 col-xs-12 col-ts-12"
        >
            <div
                class="card"
                style="border-radius:5px;"
                v-for="order in order_histories"
                :key="order.transaction.transaksi_id"
            >
                <br />
                <br />

                <div class="row">
                    <div class="col-md-10">
                        <b>Order number: {{ order.transaction.nomor_transaksi }}</b>
                    </div>
                    <div class="col-md-2">
                        <button
                            class="btn btn-success"
                            v-if="!hasbeenTransferred(order.progresses)"
                            @click="confirmPayment(order.transaction.transaksi_id, order.transaction.nomor_transaksi)"
                        >Confirm payment</button>
                        <button
                            class="btn btn-danger"
                            v-if="!hasShipped(order.progresses)"
                            @click="deleteTransaction(order.transaction.transaksi_id)"
                        >Cancel Transaction</button>
                    </div>
                </div>

                <OrderBreadcrumb :progresses="order.progresses" />

                <div class="row">
                    <div class="col-md-4">
                        <div class="row" v-for="item in order.items" :key="item.kode_barang">
                            <div class="col-md-6">
                                <img
                                    :src="`${$axios.defaults.baseURL}assets/img/thumbnails/${item.pic}.jpg`"
                                />
                            </div>

                            <div class="col-md-6">
                                <b>Description</b>
                                <br />
                                Item : {{ item.nama }}
                                <br />
                                Code: {{ item.kode_barang }}
                                <br />
                                Qty : {{ item.qty }}
                            </div>
                        </div>
                    </div>

                    <div class="col-md-8">
                        <div class="row">
                            <div class="col-md-4">
                                <b>SPB:</b>
                                <br />Bandung, Lengkong
                            </div>

                            <div class="col-md-4">
                                <b>Ship to:</b>
                                <br />
                                <u>{{ order.transaction.nama }}</u>
                                <br />
                                {{ order.transaction.alamat }}
                                <br />
                                {{ order.transaction.provinsi_nama }}
                                <br />
                                {{ order.transaction.kota_nama }}
                                <br />
                                {{ order.transaction.kecamatan_nama }}
                                <br />
                                {{ order.transaction.kode_pos }}
                                <br />
                            </div>
                            <div class="col-md-4">
                                <b>Grand Total:</b>
                                <br />
                                {{ order.transaction.grand_total | rupiah }}
                                <br />
                                <br />
                                <b>Unique Code:</b>
                                <br />
                                {{ order.transaction.kode_unik_transfer }}
                                <br />
                                <br />
                                <b>No. Resi:</b>
                                <br />
                                {{ (order.transaction.resi ? order.transaction.resi : '-') }}
                            </div>
                        </div>
                    </div>
                </div>
                <hr />
            </div>
        </li>
    </ul>
</template>

<script>
export default {
    // middleware: ['traffics'],
    layout: 'products',
    components: {
        OrderBreadcrumb: () => import('@/components/OrderBreadcrumb.vue')
    },
    data() {
        return {
            order_histories: [],
            transactions: [],
            order_progress: [],
            order_items: []
        }
    },
    methods: {
        getOrderHistory: function() {
            const user_data = JSON.parse(localStorage.getItem('user_data'))

            this.$axios
                .post(`transaction/history`, {
                    email: user_data.email
                })
                .then((response) => {
                    if (response.data.data != 0) {
                        let result = response.data.data
                        this.order_histories = result
                    }
                })
                .catch((e) => {
                    console.log(e)
                })
        },
        confirmPayment: function(transaction_id, transaction_number) {
            this.$swal({
                title: 'Confirm payment',
                text: 'Have you made the payment?',
                type: 'question',
                showCancelButton: true,
                // confirmButtonClass: "btn-danger",
                // confirmButtonColor: "#3085d6",
                confirmButtonText: 'Yes',
                cancelButtonText: 'No'
                // cancelButtonColor: "#d33",
            }).then((isConfirm) => {
                if (isConfirm.value) {
                    this.$swal({
                        // title: "",
                        text: 'Processing',
                        allowEscapeKey: false,
                        allowOutsideClick: false,
                        onOpen: () => {
                            this.$swal.showLoading()
                        }
                    })

                    this.$axios
                        .get(`transaction/confirm-payment/${transaction_id}`)
                        .then((response) => {
                            if (response.data.data == 1) {
                                this.$swal({
                                    // title: "",
                                    text: 'Payment confirmed',
                                    type: 'success'
                                }).then(() => {
                                    window.open(
                                        'https://api.whatsapp.com/send?phone=628112288143&text=Halo!%0ASaya%20sudah%20membayar%20untuk%20No%20Transaksi%20' +
                                            transaction_number,
                                        '_blank'
                                    )
                                })

                                this.getOrderHistory()
                            }
                        })
                        .catch((e) => {
                            console.log(e)

                            this.$swal({
                                title: 'Oops!',
                                text:
                                    'Cannot connect to the server, please try again later',
                                type: 'error',
                                onOpen: () => {
                                    this.$swal.hideLoading()
                                }
                            })
                        })
                }
            })
        },
        hasbeenTransferred(order_progress) {
            return order_progress.find(
                (element) => element.keterangan == 'TRANSFERRED'
            )
        },
        hasShipped(order_progress) {
            return order_progress.find(
                (element) => element.keterangan == 'SHIPPED'
            )
        },
        deleteTransaction: function(transaction_id) {
            this.$swal({
                title: 'Cancelation Confirmation',
                text: 'Arey you sure want to cancel this transaction?',
                type: 'question',
                showCancelButton: true,
                // confirmButtonClass: "btn-danger",
                // confirmButtonColor: "#3085d6",
                confirmButtonText: 'Yes',
                cancelButtonText: 'No'
                // cancelButtonColor: "#d33",
            }).then((isConfirm) => {
                if (isConfirm.value) {
                    this.$swal({
                        // title: "",
                        text: 'Processing',
                        allowEscapeKey: false,
                        allowOutsideClick: false,
                        onOpen: () => {
                            this.$swal.showLoading()
                        }
                    })

                    this.$axios
                        .get(`transaction/${transaction_id}/delete`)
                        .then((response) => {
                            if (response.data.data == 1) {
                                this.$swal({
                                    // title: "",
                                    text: 'Transaction cancelled',
                                    type: 'success'
                                })

                                this.getOrderHistory()
                            }
                        })
                        .catch((e) => {
                            console.log(e)

                            this.$swal({
                                // title: "",
                                text:
                                    'Cannot connect to the server, please try again later',
                                type: 'error',
                                onOpen: () => {
                                    this.$swal.hideLoading()
                                }
                            })
                        })
                }
            })
        }
    },
    created() {
        this.getOrderHistory()
    }
}
</script>