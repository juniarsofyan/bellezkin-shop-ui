<template>
    <div>
        <hr />
        <!--wrap main content-->
        <div class="main-content main-content-checkout">
            <div class="container">
                <!--page title-->
                <h3 class="custom_blog_title">#TrackShipment</h3>

                <!--checkout input form-->
                <div class="checkout-wrapp">
                    <!--payment method-->
                    <div class="payment-method-wrapp">
                        <div class="payment-method-form checkout-form">
                            <div class="row-col-1 row-col">
                                <div class="payment-method">
                                    <label class="text">Enter waybill number</label>
                                    <br />
                                    <input type="text" v-model="receipt_number" />
                                    <button class="button btn-pay-now" @click="checkWaybill()">Check</button>
                                </div>
                            </div>
                            <div class="row-col-12 row-col">
                                <div v-if="tracking.data">
                                    <table>
                                        <thead>
                                            <tr>
                                                <th><b>Waybill number</b></th>
                                                <th><b>Courier</b></th>
                                                <th><b>Services</b></th>
                                                <th><b>Date of Shipment</b></th>
                                                <th><b>Origin</b></th>
                                                <th><b>Destination</b></th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr>
                                                <td>{{ tracking.data.summary.waybill_number }}</td>
                                                <td>{{ tracking.data.summary.courier_code | capitalize }}</td>
                                                <td>{{ tracking.data.summary.service_code }}</td>
                                                <td>{{ tracking.data.summary.waybill_date }}</td>
                                                <td>{{ tracking.data.summary.origin }}</td>
                                                <td>{{ tracking.data.summary.destination }}</td>
                                            </tr>
                                        </tbody>
                                    </table>

                                    <table>
                                        <thead>
                                            <tr>
                                                <th><b>Shipper</b></th>
                                                <th><b>Consignee</b></th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr>
                                                <td>{{ tracking.data.details.shippper_name }}</td>
                                                <td>{{ tracking.data.summary.receiver_name }}</td>
                                            </tr>
                                            <tr>
                                                <td>{{ tracking.data.details.shipper_address1 }}</td>
                                                <td>{{ tracking.data.details.receiver_address1 }}</td>
                                            </tr>
                                        </tbody>
                                    </table>

                                    <table>
                                        <thead>
                                            <tr>
                                                <th colspan="2"><b>History</b></th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr>
                                                <td>{{ tracking.data.details.waybill_date }} {{ tracking.data.details.waybill_time }}</td>
                                                <td>SHIPMENT RECEIVED BY {{ tracking.data.summary.courier_code | capitalize }} COUNTER AT ({{ tracking.data.summary.origin }})</td>
                                            </tr>
                                            <template v-if="tracking.data.manifest">
                                                <tr v-for="track in tracking.data.manifest" :key="track.manifest_code">
                                                    <td>{{ track.manifest_date }} {{ track.manifest_time }}</td>
                                                    <td>{{ track.manifest_description }} ({{ track.city_name }})</td>
                                                </tr>
                                            </template>
                                        </tbody>
                                    </table>

                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    layout: 'products',
    data() {
        return {
            receipt_number: '',
            tracking: []
        }
    },
    filters: {
        capitalize: function (value) {
            if (!value) return ''
            value = value.toString()
            return value.toUpperCase()
        }
    },
    methods: {
        checkWaybill: function() {
            this.$axios
                .post(`ongkir/delivery-status`, {
                    receipt_number: this.receipt_number
                })
                .then((response) => {
                    if (response.data.status == 'success') {
                        this.tracking = response.data
                    } else {
                        this.$swal({
                            title: "Oops!",
                            text: "Receipt number not found",
                            type: "warning",
                        })
                    }
                })
                .catch((e) => {
                    console.log(e)
                })
        }
    }
}
</script>