<template>
    <vue-google-autocomplete v-model="value" ref="address" id="map" classname="form-control"
        placeholder="Please type your address" v-on:placechanged="getAddressData" @keydown.enter="searchAddress">
    </vue-google-autocomplete>
    <button class="button" @click="searchAddress">Search</button>
</template>

<script>
import VueGoogleAutocomplete from "vue-google-autocomplete";
export default {
    components: { VueGoogleAutocomplete },

    data: function () {
        return {
            address: "",
            addressData: "",
            value: "",
            addressList: [],
            lat: null,
            lng: null,
        };
    },

    mounted() {
        // To demonstrate functionality of exposed component functions
        // Here we make focus on the user input
        this.$refs.address.focus();
    },
    methods: {
        /**
         * When the location found
         * @param {Object} addressData Data of the found location
         * @param {Object} placeResultData PlaceResult object
         * @param {String} id Input container ID
         */
        getAddressData: function (addressData, placeResultData, id) {
            this.addressData = addressData;
            this.address = placeResultData.formatted_address;

            const location = placeResultData.geometry.location;
            this.lat = location.lat();
            this.lng = location.lng();
        },

        searchAddress() {
            if (this.address !== "" && this.address != null) {
                var addressObj = {
                    id: this.addressList.length,
                    address: this.address,
                    lat: this.lat,
                    lng: this.lng,
                }

                this.$emit("address-selected", addressObj);
                this.$refs.address.$el.blur();
                this.address = "";
                this.$nextTick(() => {
                    this.$parent.getTimezone(addressObj.lat, addressObj.lng);// Call getTimeAndTimezone method in the parent component
                    this.$parent.getTime();
                });
            }
            this.$refs.address.clear();

        },
    },
};
</script>

<style>
.form-control {
    height: 40px;
    width: 40%;
    border-radius: 10px;
}
</style>