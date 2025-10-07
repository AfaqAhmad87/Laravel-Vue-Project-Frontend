<script setup>
import { onMounted, ref } from 'vue';

const display = ref([]);
const error = ref('');
const loading = ref(true);
const ids=ref(null)
const edit=ref(false);
const alert=ref(false);
const add=ref(false);
const newProfile=ref({
  'name':"",
  'description':"",
  'age':""
});
const editProfile=ref({
   'name':"",
  'description':"",
  'age':""
});
function addProfiles(){
  fetch("/api/addProfiles",{
    method:'POST',   
    headers: {
      "Content-Type": "application/json",
    },
    body:JSON.stringify(newProfile.value)
  }).then((res)=>res.json()).then((data)=>{
  display.value.push(data);
   add.value=true;
         setTimeout(()=>{
      add.value=false;
    },5000);
  newProfile.value = { name: "", description: "", age: "" }
   display.value=display.value.filter((items)=>items.id!==item)
  })
  
  .catch((error)=>alert("Error Occured"));
}
function UpdateProfiles(){
 
  
  fetch(`/api/updateProfile/${ids.value}`,
    {
      method:'PUT',
         headers: {
      'Content-Type': 'application/json',
    },
      body:JSON.stringify(editProfile.value),

    })
    .then((res)=>res.json()).
    then((data)=>{
     
      ids.value=null;
      edit.value=false;
      window.location.reload();
        
    })
  
}
onMounted(async () => {
  fetch("/api/getProfiles")
    .then((res) => res.json())
    .then((res) => {
      display.value = res;
      loading.value = false;
    })
    .catch(() => (error.value = `Failed To Fetch Values`));
});
function EditProfile(item){
  // console.log('WORKING');
  
  fetch(`/api/populateProfile/${item}`).then((res)=>res.json()).then((data)=>{
    // Object.assign(editProfile.value, data.data);
     editProfile.value.name = data.data.name
     editProfile.value.description = data.data.description
     editProfile.value.age = data.data.age  
     edit.value=true;  
     ids.value=data.data.id;
    
  })
}
function deleteProfile(item){
if(confirm("Are You Sure To Delete This Profile"))
  fetch(`/api/deleteProfile/${item}`,{
    method:'DELETE'
  }).then((res)=>{
    if(res.ok){
    alert.value=true;
  
     display.value=display.value.filter((items)=>items.id!==item)
       setTimeout(()=>{
      alert.value=false;
    },5000);
    }
  }).
  then((res)=>console.log(res)
  );
}
</script>

<template>
  <div class="container">
       <div class="alert alert-success" role="alert" v-if="alert">
 "Profile Deleted SuccessFully"
</div>
  <div class="alert alert-success" role="alert" v-if="add">
 "Profie Added SuccessFully"
</div>
    <h1 class="title">Profiles</h1>
    <form class="add-form" @submit.prevent="addProfile">
  <input v-model="newProfile.name" placeholder="Name" required />
  <input v-model="newProfile.description" placeholder="Description" required />
  <input v-model="newProfile.age" type="number" placeholder="Age" required />
 <button class="btn add" @click="addProfiles">Add Profile</button>
</form>

 <div v-if="edit">
    <form class="add-form" @submit.prevent="UpdateProfiles">
  <input v-model="editProfile.name" placeholder="Name" required  name="name"/>
  <input v-model="editProfile.description" placeholder="Description" required name="description"/>
  <input v-model="editProfile.age" type="number" placeholder="Age" required name="age" />
 <button class="btn add" @click="UpdateProfiles">Update Profile</button>
</form>
 </div>
    <h1 v-if="loading" class="loading">Loading...</h1>

    <div v-else>
      <h1 v-if="error" class="error">{{ error }}</h1>

      <div v-else>
        <table class="profile-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Description</th>
              <th>Age</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in display" :key="item.id">
              <td>{{ item.name }}</td>
              <td>{{ item.description }}</td>
              <td>{{ item.age }}</td>
                 <td class="actions">
        
                <button class="btn delete" id="delete" @click="deleteProfile(item.id)">Delete</button>   ||
                 <button class="btn delete" id="delete" @click="EditProfile(item.id)">Edit</button>
               
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

</template>
<style scoped>
.add-form {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 2rem;
  padding: 1rem;
  background-color: #f5f7fa;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  align-items: center;
}

.add-form input {
  flex: 1 1 200px; 
  padding: 10px 12px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1rem;
  transition: border-color 0.2s;
}

.add-form input:focus {
  border-color: #0077cc;
  outline: none;
}

.btn.add {
  background-color: #0077cc;
  color: #fff;
  border: none;
  padding: 10px 18px;
  font-size: 1rem;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.btn.add:hover {
  background-color: #005fa3;
}

@media (max-width: 600px) {
  .add-form {
    flex-direction: column;
    gap: 10px;
  }

  .btn.add {
    width: 100%;
  }
}

.container {
  max-width: 900px;
  margin: 2rem auto;
  padding: 1.5rem;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.title {
  text-align: center;
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 1.5rem;
  color: #333;
}

.loading {
  text-align: center;
  color: #777;
  font-size: 1.2rem;
}

.error {
  text-align: center;
  color: #d9534f;
  font-weight: bold;
  margin-bottom: 1rem;
}

.profile-table {
  width: 100%;
  border-collapse: collapse;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.05);
  border-radius: 8px;
  overflow: hidden;
}

.profile-table th,
.profile-table td {
  border: 1px solid #e0e0e0;
  padding: 12px 16px;
  text-align: left;
}

.profile-table th {
  background-color: #0077cc;
  color: white;
  font-weight: 600;
  text-transform: uppercase;
  font-size: 0.9rem;
}

.profile-table tr:nth-child(even) {
  background-color: #f9f9f9;
}

.profile-table tr:hover {
  background-color: #eef6ff;
  transition: background-color 0.2s ease;
}

.profile-table td {
  color: #333;
  font-size: 0.95rem;
}
#delete{
  background: #f53c3c;
  color: #fff;
  padding: 10px;
  border: none;
  outline: none;
  border-radius: 5px;
}
.add-form {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 2rem;
  padding: 1rem;
  background-color: #f5f7fa;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  align-items: center;
}

.add-form input {
  flex: 1 1 200px; 
  padding: 10px 12px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1rem;
  transition: border-color 0.2s;
}

.add-form input:focus {
  border-color: #0077cc;
  outline: none;
}

.btn.add {
  background-color: #0077cc;
  color: #fff;
  border: none;
  padding: 10px 18px;
  font-size: 1rem;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.btn.add:hover {
  background-color: #005fa3;
}

@media (max-width: 600px) {
  .add-form {
    flex-direction: column;
    gap: 10px;
  }

  .btn.add {
    width: 100%;
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
