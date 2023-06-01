<template>
    <div class="wrapper">
        <Search class="location" @address-selected="handleAddressSearched" />
        <button class="button" @click="getCurrentLocation">Get current location</button>
    </div>

    <p id="address">Current Address: {{ address }}</p>
    <div v-if="isLoadingAddress" class="loading-indicator">Loading...</div>

    <GoogleMap class="map" :center="{ lat: clicked === 1 ? lat : curLat, lng: clicked === 1 ? lng : curLng }" :zoom="zoom">
        <Marker :options="{ position: { lat: curLat, lng: curLng } }" v-if="isLoadingMarker" />
        <CustomMarker :options="markerOptions(addressObj)" v-for="(addressObj, id) in addressList" :key="id">
            <img :src="lastSearch(id, addressObj) ? '/public/pin.png' : '/public/placeholder.png'" width="50" height="50" />
        </CustomMarker>
    </GoogleMap>

    <Table :address-list="addressList" />
</template>

<script>
import { GoogleMap, Marker, CustomMarker } from "vue3-google-map";
import Search from "./Search.vue";
import Table from "./Table.vue";

export default {
    components: { GoogleMap, Marker, Search, Table, CustomMarker },
    data() {
        return {
            lat: 49.263569,
            lng: -123.138573,
            curLat: 49.263569,
            curLng: -123.138573,
            isLoadingMarker: false,
            zoom: 12,
            address: "",
            value: "",
            isLoadingAddress: false,
            current: "",
            addressList: [],
            clicked: -1

        }
    },
    methods: {
        // Get current location coordinates
        getCurrentLocation() {
            if (navigator.geolocation) {
                this.clicked = 0;
                this.isLoadingAddress = true; // Show loading indicator
                this.isLoadingMarker = false; // every time clicked getCurrentLocation reset icon display to false;
                navigator.geolocation.getCurrentPosition(
                    position => {
                        this.zoom = 16;
                        console.log(position.coords.latitude);
                        this.lat = position.coords.latitude;
                        this.lng = position.coords.longitude;
                        this.curLat = position.coords.latitude;
                        this.curLng = position.coords.longitude;
                        this.isLoadingMarker = true;

                        this.getAddressFromLatLng(this.curLat, this.curLng);
                    },
                    error => {
                        console.error('Error getting current location:', error);
                    },
                    {
                        enableHighAccuracy: true,
                        timeout: 10000,
                    }
                );
            } else {
                console.error('Geolocation is not supported by this browser.');
            }
        },
        // Convert coordinates to address
        getAddressFromLatLng(curLat, curLng) {
            const geocoder = new google.maps.Geocoder();
            const latLng = new google.maps.LatLng(curLat, curLng);

            geocoder.geocode({ location: latLng }, (results, status) => {
                this.isLoadingAddress = false; // found address, stop loading
                if (status === 'OK') {
                    if (results[0]) {
                        this.address = results[0].formatted_address;
                        this.storeCurrent(this.address, curLat, curLng);
                    } else {
                        console.error('No results found');
                    }
                } else {
                    console.error('Geocoder failed due to:', status);
                }
            });
        },
        storeCurrent(address, lat, lng) {
            if (address.length) {
                // store the data in localStorage
                localStorage.setItem("current", address);
                localStorage.setItem("currentLat", lat);
                localStorage.setItem("currentLng", lng);

            }
        },
        // Handle selected address from Search component
        handleAddressSearched(addressObj) {
            this.clicked = 1;
            console.log(1);
            // check last object, if same, do nothing
            const lastAddress = this.addressList[this.addressList.length - 1];
            if (lastAddress && lastAddress.lat === addressObj.lat && lastAddress.lng === addressObj.lng) {
                return; // Last address is the same, do nothing
            }

            this.addressList.push(addressObj);
            localStorage.setItem("addressList", JSON.stringify(this.addressList));

        },
        markerOptions(addressObj) {
            return {
                position: {
                    lat: addressObj.lat,
                    lng: addressObj.lng,
                },
            };
        },
        // Last Search location with different marker
        lastSearch(id, addressObj) {
            this.zoom = 16;
            this.lat = addressObj.lat;
            this.lng = addressObj.lng;
            return id !== this.addressList.length - 1 // skip the last element
        },
    },
    mounted() {
        // Clear localStorage before unloading the page
        window.addEventListener('beforeunload', () => {
            localStorage.clear(); // Clear the localStorage
        });
    },
};
</script>

<style>
.wrapper {
    width: 100%;
    height: 100px;
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    justify-content: center;
}

.map {
    width: 80%;
    height: 700px;
    margin: auto;
}

.location {
    height: 40px;
    width: 25%;
}

.button {
    height: 45px;
    width: 5%;
    border-radius: 3px;
    margin: 0rem 0.5rem;
}

#address {
    display: flex;
    justify-content: center;
    font-size: 2rem;
}

.loading-indicator {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 50px;
    font-size: 1.8rem;
    color: red;
}
</style>
