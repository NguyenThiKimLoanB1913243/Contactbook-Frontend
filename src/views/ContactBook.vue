<template>
    <div class="page row">
        <div class="col-md-10">
            <InputSearch v-model="searchText" />
        </div>
        <div class="mt-3 col-md-6">
            <h4>Danh bạ <i class="fas fa-address-book"></i></h4>
            <ContactList v-if="fillteredContactsCount > 0" :contacts="fillteredContacts"
                v-model:activeIndex="activeIndex" />
            <p v-else>Không có liên hệ nào.</p>
            <div class="mt-3 row justify-content-around align-items-center">
                <button class="btn btn-sm btn-primary" @click="refreshList()">
                    <i class="fas fa-redo"></i> Làm mới
                </button>

                <button class="btn btn-sm btn-success" @click="goToAddContact">
                    <i class="fas fa-plus"></i> Thêm mới 
                </button>

                <button class="btn btn-sm btn-danger" @click="removeAllContacts">
                    <i class="fas fa-trash"></i> Xóa tất cả
                </button>
            </div>
        </div>
        <div class="mt-3 col-md-6">
            <div v-if="activeContact">
                <h4>
                    Chi tiết Liên hệ
                    <i class="fas fa-address-card"></i>
                </h4>
                <ContactCard :contact="activeContact" />
                <router-link :to="{
                    name: 'contact.edit',
                    params: { id: activeContact._id },
                }">
                    <span class="mt-2 badge badge-warning">
                        <i class="fas fa-edit"></i> Hiệu chỉnh</span>
                </router-link>
            </div>
        </div>
    </div>
</template>

<script>
import InputSearch from '../components/InputSearch.vue';
import ContactList from '../components/ContactList.vue';
import ContactCard from '../components/ContactCard.vue';
import ContactService from "../services/contact.service";
export default {
    components: {
        InputSearch,
        ContactCard,
        ContactList,
    },

    data() {
        return {
            contacts: [],
            activeIndex: -1,
            searchText: "",
        };
    },

    watch: {
        //Gima sat su thay doi cua searchText
        //Bo chon phan tu dang duoc chon
        searchText() {
            this.activeIndex = -1;
        }
    },
    computed: {
        //chuyen doi doi tuong contact -> chuoi de tien tim kiem
        contactString() {
            return this.contacts.map((contact) => {
                const { name, email, address, phone } = contact;
                return [name, email, address, phone].join("");
            })
        },

        //tra ve cac contact chua thong tin can tim kiem
        fillteredContacts() {
            if (!this.searchText) return this.contacts;
            return this.contacts.filter((_count, index) =>
                this.contactString[index].includes(this.searchText))
        },
        activeContact() {
            if (this.activeIndex < 0) return null;
            return this.fillteredContacts[this.activeIndex];
        },
        fillteredContactsCount() {
            return this.fillteredContacts.length;
        }
    },
    methods: {
        async retrieveContacts() {
            try {
                this.contacts = await ContactService.getAll();
            }
            catch (error) {
                console.log(error);
            }
        },
        refreshList() {
            this.retrieveContacts();
            this.activeIndex = -1;
        },
        async removeAllContacts() {
            if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
                try {
                    await ContactService.deleteAll();
                    this.refreshList();
                }
                catch (error) {
                    console.log(error);
                }
            }
        },

        goToAddContact() {
            this.$router.push("/add" );
        }
    },
    mounted() {
        this.refreshList();
    },
};
</script>

<style>
.page {
    text-align: left;
    max-width: 750px;
}
</style>