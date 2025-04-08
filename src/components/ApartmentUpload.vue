<script>
export default {
    data() {
        return {
            apartment: {
                name: "",
                type_id: "",
                max_capacity: 1,
                description: "",
                price_per_night: ""
            },
            types: [],
            uploadError: "",
            successMessage: ""
        };
    },
    created() {
        this.fetchTypes();
    },
    methods: {
        fetchTypes() {
            fetch("http://127.0.0.1:8000/api/types")
                .then(response => response.json())
                .then(data => {
                    this.types = data;
                })
                .catch(error => {
                    console.error("Hiba a típusok lekérésekor:", error);
                });
        },

        uploadApartment() {
            fetch("http://127.0.0.1:8000/api/apartments", {
                method: "POST",
                headers: {
                "Content-Type": "application/json"
            },
                body: JSON.stringify({ "name": this.apartment.name, "type_id": this.apartment.type_id, "max_capacity": this.apartment.max_capacity, "description": this.apartment.description, "price_per_night": this.apartment.price_per_night })
            }).then(response => response.json())
            .then(console.log('siker'))
    }
}
};
</script>

<template>
    <div class="container mt-5">
        <h2>Szálláshely Feltöltés</h2>
        <div v-if="uploadError" class="alert alert-danger">{{ uploadError }}</div>
        <div v-if="successMessage" class="alert alert-success">{{ successMessage }}</div>
        <form @submit.prevent="uploadApartment">
            <div class="mb-3">
                <label class="form-label">Szálláshely neve:</label>
                <input type="text" v-model="apartment.name" class="form-control" required>
            </div>
            <div class="mb-3">
                <label class="form-label">Típus:</label>
                <select v-model="apartment.type_id" class="form-control" required>
                    <option v-for="type in types" :key="type.id" :value="type.id">{{ type.name }}</option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Maximális kapacitás:</label>
                <input type="number" v-model="apartment.max_capacity" class="form-control" required>
            </div>
            <div class="mb-3">
                <label class="form-label">Leírás:</label>
                <textarea v-model="apartment.description" class="form-control"></textarea>
            </div>
            <div class="mb-3">
                <label class="form-label">Éjszakánkénti ár:</label>
                <input type="number" step="0.01" v-model="apartment.price_per_night" class="form-control" required>
            </div>
            <div class="mb-3">
                <label class="form-label">:</label>
                <input type="number" step="0.01" v-model="apartment.price_per_night" class="form-control" required>
            </div>
            <button type="submit" class="btn btn-primary">Feltöltés</button>
        </form>
    </div>
</template>

<style>
.card {
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
</style>
