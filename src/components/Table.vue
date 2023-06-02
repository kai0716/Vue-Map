<template>
    <div class="wrapper">
        <table class="table">
            <thead class="head">
                <tr class="row">
                    <th class="title-checkbox">
                        <input type="checkbox" id="selectAll" v-model="selectAll" @change="toggleAllSelection" />
                        <label for="selectAll"></label>
                    </th>
                    <th class="title-id">ID</th>
                    <th class="title-address">Address</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in displayedAddressList" :key="index">
                    <td class="data-checkbox">
                        <input type="checkbox" :id="'checkbox-' + index" v-model="selectedItems" :value="item" />
                        <label :for="'checkbox-' + index"></label>
                    </td>
                    <td class="data-id">{{ getItemId(index) }}</td>
                    <td class="data-address">{{ item.address }}</td>
                </tr>
            </tbody>
        </table>
        <button class="delete" @click="deleteSelected">Delete</button>
        <button class="next" @click="nextPage" :disabled="!showNextButton">Next</button>
        <button class="prev" @click="prevPage" :disabled="!showPrevButton">Previous</button>
    </div>
</template>
  
<script>

export default {
    data() {
        return {
            selectedItems: [],
            currentPage: 1,
            itemsPerPage: 10
        };
    },
    props: { // receive addresslist from parents
        addressList: {
            type: Array,
            default: () => [],
        },
    },
    computed: {
        totalPages() {
            return Math.ceil(this.addressList.length / this.itemsPerPage);
        },
        displayedAddressList() {
            const startIndex = (this.currentPage - 1) * this.itemsPerPage;
            const endIndex = startIndex + this.itemsPerPage;
            const reversedList = [...this.addressList].reverse();
            return reversedList.slice(startIndex, endIndex);
        },
        showNextButton() {
            return this.currentPage < this.totalPages;
        },
        showPrevButton() {
            return this.currentPage > 1;
        }
    },
    methods: {
        deleteSelected() {
            // Remove selected items from the addressList
            this.selectedItems.forEach((item) => {
                const index = this.addressList.indexOf(item);
                if (index !== -1) {
                    this.addressList.splice(index, 1);
                }
            });

            // Clear the selectedItems array
            this.selectedItems = [];

            // Change title checkbox to back to white
            const titleCheckbox = document.querySelector('.title-checkbox input[type="checkbox"]:checked+label');
            if (titleCheckbox && getComputedStyle(titleCheckbox).backgroundColor === 'rgb(0, 0, 0)') {
                titleCheckbox.style.backgroundColor = 'white';
            }
        },
        toggleAllSelection() {
            // Toggle the selection of all items based on "Select All" checkbox state
            if (this.selectAll) {
                this.selectedItems = [...this.addressList];
            } else {
                this.selectedItems = [];
            }
        },
        nextPage() {
            if (this.currentPage < this.totalPages) {
                this.currentPage++;
            }
        },
        prevPage() {
            if (this.currentPage > 1) {
                this.currentPage--;
            }
        },
        getItemId(index) {
            return (this.currentPage - 1) * this.itemsPerPage + index + 1;
        }
    },
};
</script>
  
<style>
.wrapper {
    margin: 1rem;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
}

.table {
    border: solid 1px black;
    width: 40%;
    max-height: 680px;
    height: 680px;
}

.head {
    width: 100%;
    height: 50px;
}

.title-id {
    width: 30%;
    border-right: solid 1px black;
    border-bottom: solid 1px black;
}

.title-address {
    width: 70%;
    border-bottom: solid 1px black;
}

.title-checkbox {
    width: 10%;
    border-right: solid 1px black;
    border-bottom: solid 1px black;
}

.data-id {
    text-align: center;
    border-right: solid 1px black;
    border-top: solid 1px black;
    height: 60px;
}

.data-address {
    text-align: center;
    border-top: solid 1px black;
}

.data-checkbox {
    text-align: center;
    border-right: solid 1px black;
    border-top: solid 1px black;
    height: 60px;
}

.title-checkbox input[type="checkbox"],
.data-checkbox input[type="checkbox"] {
    display: none;
}

.title-checkbox label,
.data-checkbox label {
    display: inline-block;
    width: 16px;
    height: 16px;
    background-color: white;
    border: 1px solid black;
    cursor: pointer;
}

.title-checkbox input[type="checkbox"]:checked+label,
.data-checkbox input[type="checkbox"]:checked+label {
    background-color: black;
}

.next,
.prev {
    margin: 0rem 0.5rem;
    height: 50px;
    font-size: 1rem;
    width: 100px;
    background-color: lightblue;
    border-radius: 5px;
}

.delete {
    margin: 0rem 0.5rem;
    height: 50px;
    font-size: 1rem;
    width: 100px;
    background-color: salmon;
    border-radius: 5px;
}
</style>
  