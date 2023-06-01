<template>
    <div class="table">
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Address</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in addressList" :key="index">
                    <td>{{ index }}</td>
                    <td>{{ item.address }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
  
<script>

export default {
    data() {
        return {
        };
    },
    props: {
        addressList: {
            type: Array,
            default: () => [],
        },
    },
};
</script>
  
<style></style>
  