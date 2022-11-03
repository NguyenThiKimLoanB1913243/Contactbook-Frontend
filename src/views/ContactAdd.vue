<template>
    <div div v-if="contact" class="page">
        <h4>Thêm mới một liên hệ</h4>
        <ContactForm
            :contact="contact"
            @submit:contact="addContact"
            @delete:contact="deleteContact"
        />
        <p>{{ message }}</p>
    </div>
</template>

<script>
import ContactForm from "../components/ContactForm.vue";
import ContactService from "../services/contact.service";

export default {
    components: {
        ContactForm,
    },
    data() {
        return {
            contact: {
                name: "",
                email: "",
                address: "",
                phone: "",
                favorite: false
            },
            message: "",
        };
    },
    methods: {
        initContact(){},
        async addContact(data) {
            console.log("input: ", data);
            const [error, response] = await this.handle(
                ContactService.create(data)
            );
            if (error) {
                console.log(error);
            } else {
                console.log(response.data);
                this.message = "Liên hệ được thêm thành công.";
            }
        },
        resetContact() {
        alert("reset");
        },
    },
    mounted() {
        this.message = "";
        this.initContact();
    },
};
</script>