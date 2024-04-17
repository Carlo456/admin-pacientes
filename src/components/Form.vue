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
    name: '',
    email: '',
    description: '',
    image: null,
    register_date: '',
});

const handleItemSubmit = () => {
    item.register_date = new Date();
    
    //turn the object into raw object
    let rawFormInfo = toRaw(item);
    //validations
    let isFormInfoValid = formValidations(rawFormInfo);
    //set the reactive to show messages about whats wrong
    alert.type = isFormInfoValid.alertValues.type;
    alert.message = isFormInfoValid.alertValues.message;
    
    //if the info is valid append it from the reactive, maybe sanitization would be next
    if (isFormInfoValid.valid) {
        const formData = new FormData();
        formData.append('id', '');
        formData.append('name', item.name);
        formData.append('email', item.email);
        formData.append('description', item.description);
        formData.append('image', item.image);
        formData.append('register_date', item.register_date);

        console.log("From formData: ", formData)
        //TODO: make the HTTP request to my C# Server and follow tutorial 
    }   
}

const onFileChange = (event) => {
    item.image = event.target.files[0];
}

const formValidations = (rawFormObject) => {
    console.log("Here will the validations appear: ",rawFormObject);
    
    let alertValues = {
        type: '',
        message: ''
    }
    if(Object.values(rawFormObject).some(value => value === '')){
        alertValues.type = 'Error';
        alertValues.message = 'All fields in form are mandatory';
        return { 
            valid: false,
            alertValues
        }
    }
    for(let [key, value] of Object.entries(rawFormObject)){
        if (key === 'name') {
            if(value.length <= 3 || value.length > 50){
                alertValues.type = 'Error';
                alertValues.message = 'The name is too long or too short...';
                return { 
                    valid: false,
                    alertValues
                }
            }
        }
        if (key === 'email') {
            if (!validEmail(value)) {
                alertValues.type = 'Error';
                alertValues.message = 'Wrong or invalid email';
                return { 
                    valid: false,
                    alertValues
                }   
            }
        }
        if (key === 'description') {
            if(value.length <= 3 || value.length > 200){
                alertValues.type = 'Error';
                alertValues.message = 'The description is too long or too short...';
                return { 
                    valid: false,
                    alertValues
                }
            }
        }
        if(key === 'image'){
            if (value === null) {
                alertValues.type = 'Error';
                alertValues.message = 'You must insert an image in the form.';
                return { 
                    valid: false,
                    alertValues
                }
            }
            if (!validImageType(value.type)){
                alertValues.type = 'Error';
                alertValues.message = 'Invalid image type, only JPEG, JPG and PNG allowed';
                return { 
                    valid: false,
                    alertValues
                }
            }
        }
    }
    alertValues.type = 'Success';
    alertValues.message = 'Information uploaded correctly.';
    return { 
        valid: true,
        alertValues
    }
}

const validImageType = (fileType) => {
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

const validEmail = (email) => {
    const regex = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
    return regex.test(email);
}    


const validImageSize = (fileSize) => {
    let validSize = false;
    if (fileSize < 8000 || fileSize < 1600) {
       validSize = true;
    }
    return validSize;
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
            <div class="mb-5 hidden">
                <label for="register_date" class="block text-gray-700 uppercase font-bold">Register date</label>
                <input id="register_date" type="date" v-model="item.register_date" placeholder="Register date" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <input type="submit" class="bg-indigo-600 w-full p-3 text-white uppercase font-bold hover:bg-indigo-700 cursor-pointer transition-colors" value="Register patient"/>
        </form>
    </div>
</template>

<style scoped>

</style>
