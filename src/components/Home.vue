<template>
    <div>
        <!--Navbar-->
        <div class="has-background-dark p-3 has-text-info">
            Hatchways Assignment
        </div>
        <!--Navbar-->

        <!--Main-->
        <div class="columns m-0">
            <div class="column m-0"></div>
            <div class="column m-0 is-6">

                <!--Searchbars-->
                <div class="p-3">
                    <input type="text" v-model="searchByName" placeholder="Search by Name" class="custom-input mb-4">
                    <input type="text" v-model="searchByTag" placeholder="Search by Tag" class="custom-input">
                </div>
                <!--Searchbars-->

                <!--Display Area-->
                <div class="p-3 mt-4" style="height:700px; overflow-y:auto">
                    
                    <div v-for="(student,index) in filteredList" :key="index" 
                    class="p-3 mb-4 is-rounded has-shadow has-text-dark has-background-light flex">
                        <div class="pl-3 pr-3 mr-5">
                            <div style="border: 4px solid #d5d5d5; border-radius:50%; padding:20px" class="has-custom-background">
                                <img width="100" height="100" :src="student.pic" :alt="student.email" >
                            </div>
                        </div>
                        <div class="ml-3 w-100">
                            <div class="flex">                            
                                <div class="is-size-3 w-90">{{student.firstName}} {{student.lastName}}</div>
                                <div class="has-text-right is-size-3 is-clickable" @click="toggleGrades(index)" >+</div>
                            </div>
                            <p><strong>Email</strong>: {{student.email}}</p>
                            <p><strong>Company</strong>: {{student.company}}</p>
                            <p><strong>Skill</strong>: {{student.skill}}</p>
                            <p><strong>Average</strong>: {{calculateAverage(student.grades)}}%</p>
                            <div :id="'testScores'+index" style="display:none">
                                <p v-for="(grade,index2) in student.grades" :key="index2">
                                    <strong>Test {{index2+1}} </strong>: {{grade}}
                                </p>
                            </div>
                            <p v-if="student.tags" class="mt-3 mb-3">
                                <span v-for="(tag,index3) in student.tags.split(' ')" :key="index3" >
                                    <span v-if="tag.length>0" class="mr-3" 
                                style=" padding: 3px;
                                background-color: gold;
                                color: black;
                                border: 1px solid gold;
                                border-radius: 3px;">
                                        {{tag}}
                                    </span>
                                </span>
                            </p>
                            <p>
                                <input type="text" placeholder="Add Tag.." class="tag-input" :id="student.email+'tag'">
                                <button @click="addTag(student.email)" class="tag-button">Add</button>
                            </p>
                        </div>
                    </div>
                </div>
                <!--Display Area-->
            </div>
            <div class="column m-0"></div>
        </div>
        <!--Main-->

        <!--Footer-->
        <div class="has-text-right pl-3">
            Made By Nishad Raisinghani
        </div>
        <!--Footer-->
    </div>
</template>
<script>
import axios from 'axios';
export default {
    name:"Home",
    data() {
        return {
            students:[],
            searchByName:"",
            searchByTag:"",
            recompute:0
        }
    },
    methods: {
        getData(){
            var self=this
            var url = "https://api.hatchways.io/assessment/students"
            axios.get(url)
            .then((response)=>{

                self.students=response.data['students'];
                for(var i=0;i<self.students.length;i++){
                    self.students[i]['tags']=""
                }
            
            })
            .catch((err)=>{
                alert('Error Occured ' + err)
            })
        },
        calculateAverage(grades){
            var sum=0
            var numberOfSubjects=grades.length
            for(var i of grades){
                sum+= parseInt(i)
            }
            var average=sum/numberOfSubjects
            return average
        },
        toggleGrades(index){
            var element = document.getElementById("testScores"+index)
            if(element.style.display==="none"){
                element.style.display="block"
            }
            else{
                element.style.display="none"
            }
        },
        addTag(email){
            var tag = document.getElementById(email+"tag").value
            for(var i=0;i<this.students.length;i++){
                if(this.students[i].email==email){
                    if (!('tags' in this.students[i])){
                        this.students[i]['tags']=[""]
                        this.students[i]['tags']+=" "+ tag
                    }
                    else{
                        this.students[i]['tags']+=" "+ tag
                    }
                    console.log(this.students[i])
                }
            }
            document.getElementById(email+"tag").value=""
            this.recompute++;
        }
    },
    mounted() {
        this.getData();
    },
    computed: {
    filteredList() {
        this.recompute
        return this.students.filter(student => {
            if((student.firstName+student.lastName).toLowerCase().includes(this.searchByName.toLowerCase())){
                if(student.tags.toLowerCase().includes(this.searchByTag.toLowerCase())){
                    return student  
                }
            }
        })
        }
    }
}
</script>