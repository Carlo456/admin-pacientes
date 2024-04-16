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
    register_date: '',
    description: '',
    image: []
});

//refs
const filesArray = ref([]);

const handlePatientSubmit = () => {
    const itemForm = new FormData();
    Object.entries(item).forEach(([key, value]) => {
        itemForm.append(key, value);
    });
    console.log("From submit", itemForm);
    formValidation(itemForm);
}

//just to check and set the dropEffect
const handleDragOver = (event) => {
    event.dataTransfer.dropEffect = 'copy';
}

const handleDrop = (event) => {
    event.stopPropagation();
    processFiles(event.dataTransfer.files); 
}

const onFileChange = (event) => {
    processFiles(event.target.files);
}

const processFiles = (files) => {
    for (let index = 0; index < files.length; index++) {
        filesArray.value.push(files.item(index));
    }
    let filteredFileArray = removeDuplicateFiles(filesArray.value);
    //TODO: use the ref to show the preview of the files to user
    filesArray.value = filteredFileArray;
    console.log("From value",filesArray.value)
    item.image = filteredFileArray;
    console.log("From reactive:", item.image)
}

const removeDuplicateFiles = (files) => {
    let newFilesArray = files.filter((file, index, self) => {
        return self.findIndex(f => f.name === file.name && f.size === file.size && imageType(file.type)) === index;  
    });
    return newFilesArray;
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

const formValidation = (item) => {
    let hasEmptyFields = false;
    let incorrectEmail = false;

    let algo = toRaw(item);
    console.log("dont understand this: ",algo)
    console.log(typeof algo)

    console.log("dont understand this 2: ",item)
    console.log(typeof item)


    //TODO: just find out test cases that might break the validation images for example and pass them as validating functions for class
    for (let [key, value] of algo.entries()) {
        if (!value.trim()) {
            alert.type = "Error"
            alert.message = `Some form fields are missing...`;
            hasEmptyFields = true;
        }
        if(key === 'email' && !validEmail(value)){
            alert.type = "Error"
            alert.message = `Incorrect or inconsistent email...`;
            incorrectEmail = true;
        }
    }

    if (!hasEmptyFields || !incorrectEmail) {
        // All fields are filled, proceed to send the FormData
        console.log("Sending data...", item);
        // Here you would typically send formData to a server via fetch or XMLHttpRequest
    }
}

const validEmail = (email) => {
    const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
    return emailRegex.test(email);
}

</script>

<template>
    <div class="md:w-1/2">
        <h2 class="font-black text-3xl text-center">Item shop</h2>
        <p class="text-lg mt-5 text-center mb-10">
            Add items and 
            <span class="text-indigo-600 font-bold">Administrate them</span>
        </p>
        <form v-on:submit.prevent="handlePatientSubmit" class="bg-white shadow-md rounded-lg py-10 px-5 mb-10">
            <div class="mb-5">
                <label for="patient_name" class="block text-gray-700 uppercase font-bold">Item name</label>
                <input id="patient_name" type="text" v-model="item.name" placeholder="Name" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <div class="mb-5">
                <label for="email" class="block text-gray-700 uppercase font-bold">Email</label>
                <input id="email" type="email"  v-model="item.email" placeholder="Email" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <div class="mb-5">
                <label for="register_date" class="block text-gray-700 uppercase font-bold">Register date</label>
                <input id="register_date" type="date" v-model="item.register_date" placeholder="Register date" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md"/>
            </div>
            <div class="mb-5">
                <label for="symptoms" class="block text-gray-700 uppercase font-bold">Description</label>
                <textarea id="symptoms" type="text" v-model="item.description" placeholder="Description" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md h-40"/>
            </div>
            <div v-on:dragover.prevent="handleDragOver" v-on:drop.prevent="handleDrop" class="mb-5 border-dashed border-2 border-indigo-600 flex flex-col items-center justify-center p-5">
                <span class="font-bold pb-3">Drag or Drop File</span>
                <span class="text-indigo-600 font-bold text-2xl pb-3">OR</span>
                <label for="dropzoneFile" class="bg-indigo-600 text-white font-bold py-2 px-4 rounded cursor-pointer hover:bg-indigo-700">Select item images</label>
                <input id="dropzoneFile" class="hidden" type="file" multiple v-on:change.prevent="onFileChange"/>
            </div>
            <input type="submit" class="bg-indigo-600 w-full p-3 text-white uppercase font-bold hover:bg-indigo-700 cursor-pointer transition-colors" value="Register patient"/>
        </form>
    </div>
</template>

<style scoped>

</style>
