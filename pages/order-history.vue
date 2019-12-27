<template>
    <div>
        <div
            v-for="order in order_histories"
            :key="order.transaction.transaksi_id"
            class="list-order-history"
        >
            <div class="row">
                <div class="col-md-12">
                    <h4># {{ order.transaction.nomor_transaksi }}</h4>
                </div>
                <div class="col-md-9">
                    <table>
                        <thead>
                            <tr>
                                <th>Item</th>
                                <th>Total</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="item in order.items" :key="item.kode_barang">
                                <td class="item-order">
                                    <div class="row">
                                        <div class="col-md-2">
                                            <img
                                                :src="
                                                    `${$axios.defaults.baseURL}assets/img/thumbnails/${item.pic}.jpg`
                                                "
                                            />
                                        </div>
                                        <div class="col-md-10">
                                            {{ item.nama }}
                                            <div>
                                                <div
                                                    style="text-decoration: line-through;"
                                                >IDR 800.000</div>
                                                <span>{{ item.qty }}x</span>
                                                <span>IDR 800.000</span>
                                            </div>
                                        </div>
                                    </div>
                                </td>
                                <td>IDR 800.000</td>
                            </tr>
                        </tbody>
                        <tfoot>
                            <!-- <tr>
                                <td></td>
                            </tr>-->
                            <tr>
                                <td colspan align="right">
                                    <b>Subtotal</b>
                                </td>
                                <td>
                                    <b>
                                        {{
                                        order.transaction.grand_total
                                        | rupiah
                                        }}
                                    </b>
                                </td>
                            </tr>
                            <tr>
                                <td colspan align="right">
                                    <b>Unique Code</b>
                                </td>
                                <td>
                                    <b>
                                        {{
                                        order.transaction
                                        .kode_unik_transfer | rupiah
                                        }}
                                    </b>
                                </td>
                            </tr>
                            <tr>
                                <td colspan align="right">
                                    <b>Grand total</b>
                                </td>
                                <td>
                                    <b>
                                        {{
                                        order.transaction.grand_total
                                        | rupiah
                                        }}
                                    </b>
                                </td>
                            </tr>
                        </tfoot>
                    </table>
                    <div class="row" style="margin-bottom:1em">
                        <div class="col-md-12">
                            <b>SPB:</b>
                            <br />Bandung, Lengkong
                        </div>
                    </div>
                    <div class="row section-desc-order">
                        <div class="col-md-9">
                            <b>Ship to:</b>
                            <br />
                            <u>{{ order.transaction.nama }}</u>
                            <br />
                            {{ order.transaction.alamat }}
                            <br />
                            {{ order.transaction.provinsi_nama }},
                            {{ order.transaction.kota_nama }} Kec.
                            {{ order.transaction.kecamatan_nama }}
                            <br />
                            {{ order.transaction.kode_pos }}
                            <br />
                        </div>

                        <div
                            class="col-md-3 text-right"
                            style="display: flex;
                                    flex-direction: column;
                                    justify-content: space-between;"
                        >
                            <div>
                                <button
                                    v-if="!hasShipped(order.progresses)"
                                    @click="
                                        deleteTransaction(
                                            order.transaction.transaksi_id
                                        )
                                    "
                                    class="btn btn-link"
                                >Cancel Order</button>
                            </div>
                            <button
                                v-if="!hasbeenTransferred(order.progresses)"
                                @click="
                                    confirmPayment(
                                        order.transaction.transaksi_id,
                                        order.transaction.nomor_transaksi
                                    )
                                "
                                class="btn btn-success"
                            >Confirm payment</button>
                        </div>
                    </div>
                </div>
                <div class="col-md-3">
                    <section class="root">
                        <!-- <figure>
                            <figcaption>
                                <h5>Tracking Details</h5>
                                <h6>Order Number</h6>
                                <h4># {{ order.transaction.nomor_transaksi }}</h4>
                            </figcaption>
                        </figure>-->
                        <div class="order-track">
                            <div class="order-track-step disabled">
                                <div class="order-track-status">
                                    <span class="order-track-status-dot"></span>
                                    <span class="order-track-status-line"></span>
                                </div>
                                <div class="order-track-text">
                                    <span class="order-track-text-stat">Received</span>
                                </div>
                            </div>
                            <div class="order-track-step disabled">
                                <div class="order-track-status">
                                    <span class="order-track-status-dot"></span>
                                    <span class="order-track-status-line"></span>
                                </div>
                                <div class="order-track-text">
                                    <span class="order-track-text-stat">Shipped</span>
                                    <span class="order-track-text-sub">
                                        No. resi
                                        <b>
                                            {{
                                            order.transaction.resi
                                            ? order.transaction.resi
                                            : '-'
                                            }}
                                        </b>
                                    </span>
                                </div>
                            </div>
                            <div class="order-track-step disabled">
                                <div class="order-track-status">
                                    <span class="order-track-status-dot"></span>
                                    <span class="order-track-status-line"></span>
                                </div>
                                <div class="order-track-text">
                                    <span class="order-track-text-stat">Packed</span>
                                </div>
                            </div>
                            <div class="order-track-step disabled">
                                <div class="order-track-status">
                                    <span class="order-track-status-dot"></span>
                                    <span class="order-track-status-line"></span>
                                </div>
                                <div class="order-track-text">
                                    <span class="order-track-text-stat">Payment Confirmed</span>
                                </div>
                            </div>
                            <div class="order-track-step disabled">
                                <div class="order-track-status">
                                    <span class="order-track-status-dot"></span>
                                    <span class="order-track-status-line"></span>
                                </div>
                                <div class="order-track-text">
                                    <span class="order-track-text-stat">Tranfered</span>
                                </div>
                            </div>
                            <div class="order-track-step">
                                <div class="order-track-status">
                                    <span class="order-track-status-dot"></span>
                                    <span class="order-track-status-line"></span>
                                </div>
                                <div class="order-track-text">
                                    <span class="order-track-text-stat">Place Order</span>
                                </div>
                            </div>
                        </div>
                    </section>
                </div>
            </div>
        </div>
    </div>
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
    created() {
        this.getOrderHistory()
    },
    methods: {
        getOrderHistory() {
            const user_data = JSON.parse(localStorage.getItem('user_data'))

            this.$axios
                .post(`transaction/history`, {
                    email: user_data.email
                })
                .then((response) => {
                    if (response.data.data != 0) {
                        const result = response.data.data
                        this.order_histories = result
                    }
                })
                .catch((e) => {
                    console.log(e)
                })
        },
        confirmPayment(transaction_id, transaction_number) {
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
        deleteTransaction(transaction_id) {
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
    }
}
</script>
