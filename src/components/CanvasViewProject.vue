<template>
  <div class="canvas-project-details">
    <div class="m-0-0-base"><a @click="backToList">&laquo; back to projects</a></div>

    <template>

    <div id="project-details">

      <div class="flex">
        <div class="flex flex-center-both project-name">
          <div>
            <h1 class="serif fs-b1 color-w ta-center">{{project.project.name}}</h1>
            <template v-if="project.project.type == 'CustomProject'">
              <p class="color-w m-0 ta-center">Shared by {{project.project.teacher.name}}</p>
              <p class="color-w m-0 ta-center">{{project.project.teacher.school}}</p>
            </template>
          </div>
        </div>

        <template v-if="project.project.type == 'Project'">
        <div class="project-video">
          <div class="videoWrapper">
            <video controls class="videoWrapper__video" :poster="project.intro_video_poster" preload="auto">
              <template v-for="url in project.intro_video_urls_filtered">
                <source :key="url" :src="url">
              </template>
            </video>
          </div>
        </div>
        </template>

        <template v-if="project.project.type == 'CustomProject'">
          <div class="project-video">
            <div class="videoWrapper">
              <img :src="project.project.image" v-if="project.project.image_or_video=='image'">
              <video :src="project.project.video" v-else-if="project.project.image_or_video='video'" controls></video>
            </div>
          </div>
        </template>

      </div>

      <div class="sticky assign-project frame flex flex-jc-c"><a class="cbtn-primary" @click="assignProject(project)"><span>assign project</span><span>&raquo;</span></a></div>

      <div class="flex flex-jc-sb flex-reverse project-details">

        <div class="flex-col">



          <!-- DETAILS TABLE -->
          <div class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Details</h3>
            <table class="canvas-table">
            <tr v-if="project.project.outdoors || project.project.indoors || project.project.location_type == 'ON'">
              <th scope="row">Spend the time</th>
              <td v-if="project.project.location_type == 'ON'">Online</td>
              <td v-else-if="project.project.outdoors && project.project.indoors">Indoors and Outdoors</td>
              <td v-else-if="project.project.outdoors">Outdoors</td>
              <td v-else>Indoors</td>
            </tr>
            <tr v-if="project.project.topics.length > 0">
              <th scope="row">Topics</th>
              <td>{{topics}}</td>
            </tr>
            <tr v-if="project.project.activities.length > 0">
              <th scope="row">Type of Activity</th>
              <td>{{activities}}</td>
            </tr>
            <tr v-if="project.project.classroom_materials">
              <th scope="row">Classroom materials</th>
              <td>{{ project.project.classroom_materials }}</td>
            </tr>
          </table>
          </div>

          <!-- DESCRIPTION -->
          <div class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Description</h3>
                <vue-markdown :source="project.description || project.project.description"></vue-markdown>
          </div>

          <!-- RESOURCES FOR TEACHERS -->
          <div v-if="project.resource_1 && project.resource_1.url !== ''" class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Additional Resources for Teachers</h3>
            <ul class="standard">
              <li><a :href="project.resource_1.url" target="_blank">{{project.resource_1.name || project.resource_1.url}}</a></li>
              <li v-if="project.resource_2.url"><a :href="project.resource_2.url" target="_blank">{{project.resource_2.name || project.resource_2.url}}</a></li>
              <li v-if="project.resource_3.url"><a :href="project.resource_3.url" target="_blank">{{project.resource_3.name || project.resource_3.url}}</a></li>
              <li v-if="project.resource_4.url"><a :href="project.resource_4.url" target="_blank">{{project.resource_4.name || project.resource_4.url}}</a></li>
            </ul>
          </div>

          <!-- RESOURCES FOR STUDENTS -->
          <div v-if="project.student_resource_1 && project.student_resource_1.url !== ''" class="frame p-base">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Additional Resources for Students</h3>
            <ul class="standard">
              <li><a :href="project.student_resource_1.url" target="_blank">{{project.student_resource_1.name || project.student_resource_1.url}}</a></li>
              <li v-if="!!project.student_resource_2.url"><a :href="project.student_resource_2.url" target="_blank">{{project.student_resource_2.name || project.student_resource_2.url}}</a></li>
              <li v-if="!!project.student_resource_3.url"><a :href="project.student_resource_2.url" target="_blank">{{project.student_resource_3.name || project.student_resource_3.url}}</a></li>
              <li v-if="!!project.student_resource_4.url"><a :href="project.student_resource_4.url" target="_blank">{{project.student_resource_4.name || project.student_resource_4.url}}</a></li>
            </ul>
          </div>

        </div><!-- end .flex-col -->

        <div class="flex-col">

          <!-- TIME REQUIRED -->
          <div class="frame p-base div1">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Time Required</h3>
            <p class="serif fs-b1 color-b">{{project.time_required || project.project.time }}</p>
          </div>

          <!-- MATERIALS NEEDED -->
          <div class="frame p-base div2">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Materials Needed</h3>

              <template v-if="project.project.type == 'Project'">
                <ul class="standard">
                  <li v-for="(mat,i) in project.materials" :key="'mat'+i">{{ mat }}</li>
                </ul>
              </template>
              <template v-if="project.project.type == 'CustomProject'">
                <p>{{project.project.materials}}</p>
              </template>
          </div>

          <!-- IF AFFILIATE WHERE STUDENT HAS TO ENTER DATA -->
          <div v-if="!project.project.form && project.project.type == 'Project'" class="frame p-base message">
            <h3 class="color-p fs-base serif w-700 m-0-0-s4">Note for Educators</h3>
              <p>This project is not hosted on SciStarter.org. Students will need to create a project account during the assignment to log data and to receive credit for their participation. Or, for students 13 and under, you can create a project account and log data as a class to complete the assignment. Select "Students will submit data to teacher, Teacher will submit data to the project (suggested for younger students)" when assigning project to log data on behalf of your class.</p>
          </div>

        </div><!-- end .flex-col -->


      </div><!-- end .flex -->

      <div class="frame p-base m-0-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Instructions</h3>
          <ol class="instructions">
            <template v-if="project.project.type == 'Project'">
              <li v-for="(step, index) in project.instruction_steps_active" :key="'step' + index">{{step}}</li>
            </template>
            <template v-if="project.project.type == 'CustomProject'">
              <vue-markdown :source="project.project.steps"></vue-markdown>
              <!-- <li v-for="(step, index) in project.project.steps" :key="'step' + index">{{step}}</li> -->
            </template>
        </ol>
      </div>

      <template v-if="project.project.type == 'CustomProject' && project.project.notes">
          <div class="frame p-base m-base-basehalf">
              <h3 class="color-p fs-base serif w-700 m-0-0-base">Notes from the Project Creator</h3>
              <vue-markdown :source="project.project.notes"></vue-markdown>
          </div>
      </template>

      <template v-if="project.project.type == 'CustomProject'">
        <div class="frame p-base m-base-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-base">Project Questions</h3>
          <CustomForm :fields="questions"  />
        </div>
      </template>

      <template v-if="project.project.type == 'Project'">
        <div class="frame p-base m-base-basehalf">
          <h3 class="color-p fs-base serif w-700 m-0-0-s4">Project Worksheet</h3>
          <p class=" m-0-0-lg">This is the form students will fill out and submit to you</p>

          <div v-for="(item,i) in worksheet" class="customFormQuestion" :key="'q'+i">
              <h4 class="label" :class="{'heading':item.type=='heading'}">{{item.prompt}}</h4>
              <small v-if="item.extra">{{item.extra}}</small>

              <input v-if="item.type=='short-text'" type="text" disabled />
              <textarea v-else-if="item.type=='long-text'" disabled></textarea>

              <el-radio-group v-else-if="item.type==='true-false'">
                  <el-radio :value="true" disabled>Yes</el-radio>
                  <el-radio :value="false" disabled>No</el-radio>
              </el-radio-group>

              <el-select v-else-if="item.type==='select-one' && item.options" placeholder="Select One">
                  <el-option v-for="o in item.options" :key="o" :value="o" :label="o"></el-option>
              </el-select>




          </div>

        </div>
      </template>

    </div><!-- end #project-details -->

  </template>




    </div>
</template>

<script>
//*******REMOVE FOR PROD *********
import store from '../store.js'
//********************************
import VueMarkdown from 'vue-markdown'
import CustomForm from '../components/CanvasCustomForm'
export default {
  name: 'ViewProject',
  components: {
      VueMarkdown,
      CustomForm
  },
  props: ['project'],
  data: function(){
    return {
      questions: null,
      worksheet: store.worksheet
    }
  },
  methods: {
    backToList: function() {
      this.$emit('clearProject')
    },
    assignProject: function(e){
      this.$emit('assign',e)
    },
    scrollToTop(){
      window.scrollTo(0,0)
    },
    createWorksheetOptions(){
      this.worksheet.map(function(d){
        if (d.type==='select-one' && d.extra !== ''){
          d.options = d.extra.split(',')
        }
        return d;
      })
    }
  },
  computed: {
    topics(){
      let topics = []
      this.project.project.topics.forEach(d => {
        topics.push(d.label)
      })
      return topics.join(', ')
    },
    activities(){
      let act = []
      this.project.project.activities.forEach(d => {
        act.push(d.label)
      })
      return act.join(', ')
    }

  },
  mounted(){
    this.scrollToTop()
    if (this.project.project.type == 'CustomProject') {
      this.questions = JSON.parse(this.project.project.json)
    }
    if (this.project.project.type == 'Project') {
      this.createWorksheetOptions()
    }
  }
}
</script>

<style lang="scss">

</style>
