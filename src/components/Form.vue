<script setup>

import { reactive, ref, toRaw } from 'vue';

//components
import Alert from './Alert.vue';

const alert = reactive({
    type: '',
    message: ''
})

//state
const item = reactive({
    id: '',
    name: '',
    email: '',
    description: '',
    image: null,
    register_date: '',
});

//refs
const file_img = ref([]);

const handleItemSubmit = () => {
    console.log('Form sumbited: ', item)
    //TODO: Just make the validations and iterate over the formData to append the stuff after validation toRaw might work
    
    let rawFormInfo = toRaw(item);
    console.log("This is the raw form info for validation: ",rawFormInfo);

    validations(rawFormInfo);

    const formData = new FormData();
    formData.append('id', item.id);
    formData.append('name', item.name);
    formData.append('email', item.email);
    formData.append('description', item.description);
    formData.append('image', item.image);
    formData.append('dateTime', item.dateTime);

    console.log("From formData: ", formData)

    //TODO: make the HTTP request to my C# Server and follow tutorial 
}

const onFileChange = (event) => {
    item.image = event.target.files[0];
}

const validations = (rawFormObject) => {
    console.log("Here will the validations appear: ",rawFormObject);
}

const imageType = (fileType) => {
    let type = false;
    switch (fileType) {
        case 'image/jpeg':
        case 'image/png':
        case 'image/gif':
            type = true;
            return type;
        default:
            return type;
    }
}



</script>

<template>
    <div class="md:w-1/2">
        <h2 class="font-black text-3xl text-center">Item shop</h2>
        <p class="text-lg mt-5 text-center mb-10">
            Add items and 
            <span class="text-indigo-600 font-bold">Administrate them</span>
        </p>
        <form v-on:submit.prevent="handleItemSubmit" class="bg-white shadow-md rounded-lg py-10 px-5 mb-10">
            <div class="mb-5 hidden">
                <label for="item_id" class="block text-gray-700 uppercase font-bold">Id</label>
                <input id="item_id" type="text" v-model="item.id" placeholder="Id" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <div class="mb-5">
                <label for="item_name" class="block text-gray-700 uppercase font-bold">Item name</label>
                <input id="item_name" type="text" v-model="item.name" placeholder="Name" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <div class="mb-5">
                <label for="email" class="block text-gray-700 uppercase font-bold">Email</label>
                <input id="email" type="email"  v-model="item.email" placeholder="Email" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <div class="mb-5">
                <label for="description" class="block text-gray-700 uppercase font-bold">Description</label>
                <textarea id="description" type="text" v-model="item.description" placeholder="Description" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md h-40"/>
            </div>
            <div class="mb-5 border-dashed border-2 border-indigo-600 flex flex-col items-center justify-center p-5">
                <label for="file" class="bg-indigo-600 text-white font-bold py-2 px-4 rounded cursor-pointer hover:bg-indigo-700">Select item images</label>
                <input id="file" class="hidden" type="file" v-on:change.prevent="onFileChange"/>
            </div>
            <div class="mb-5">
                <label for="register_date" class="block text-gray-700 uppercase font-bold">Register date</label>
                <input id="register_date" type="date" v-model="item.register_date" placeholder="Register date" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <input type="submit" class="bg-indigo-600 w-full p-3 text-white uppercase font-bold hover:bg-indigo-700 cursor-pointer transition-colors" value="Register patient"/>
        </form>
    </div>
</template>

<style scoped>

</style>
