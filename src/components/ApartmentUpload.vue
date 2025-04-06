<script>
export default {
    data() {
        return {
            authToken: localStorage.getItem("authToken"),
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
    onFileChange(event) {
        this.apartment.image = event.target.files[0];
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
            const formData = new FormData();
            formData.append("name", this.apartment.name);
            formData.append("type_id", this.apartment.type_id);
            formData.append("max_capacity", this.apartment.max_capacity);
            formData.append("description", this.apartment.description);
            formData.append("price_per_night", this.apartment.price_per_night);

            if (this.apartment.image) {
                formData.append("image", this.apartment.image);
            }

            fetch("http://127.0.0.1:8000/api/apartments", {
                method: "POST",
                body: formData,
            })
                .then(response => response.json())
                .then(data => {
                    if (data.error) {
                        this.uploadError = data.error;
                    } else {
                        this.successMessage = "Sikeresen létrehozva!";
                        this.uploadError = "";
                    }
                })
                .catch(error => {
                    console.error("Hiba:", error);
                    this.uploadError = "Hiba történt a feltöltés során.";
                });
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
                <label class="form-label">Kép feltöltése:</label>
                <input type="file" @change="onFileChange" class="form-control">
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
