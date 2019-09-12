<template>
  <div id="wrapper">
    <div id="subjectList">
      <h1>Subjects</h1>
      <input v-model="search" placeholder="Search for a subject code" />
      <select id="optionList" v-model="selectedSubject" v-bind:size="subjects.length">
        <option
          v-for="(subject, index) in searchResults"
          :key="subject.subject_code"
          :value="index"
        >
          {{
          subject.subject_code + " — " + subject.subject_desc
          }}
        </option>
      </select>
    </div>

    <div id="studentList">
      <h1>Students</h1>
      <pre>{{ makeStudentList }}</pre>
    </div>
  </div>
</template>

<script>
import json from "../../data/sample-data.json";

export default {
  name: "Subjects",
  data: function() {
    return {
      search: "",
      json: json,
      subjects: this.parseSubjects(),
      selectedSubject: -1
    };
  },
  computed: {
    searchResults() {
      this.resetSelected();
      return this.subjects.filter(subject => {
        return subject.subject_code.includes(this.search.toUpperCase());
      });
    },

    makeStudentList() {
      var studentList = "";

      if (this.selectedSubject > -1) {
        let results = this.searchResults;
        let selected = results[this.selectedSubject];
        let students = selected.students;

        students.forEach(student => {
          studentList += `${student}\n`;
        });

        return studentList;
      }

      return null;
    }
  },
  methods: {
    resetSelected() {
      this.selectedSubject = -1;
    },

    parseSubjects() {
      var courseList = [];
      json.forEach(student => {
        let id = student.student_id;

        if (student.class_details.length == 0) {
          return;
        }

        student.class_details.forEach(subject => {
          let code = subject.subject_code,
            desc = subject.subject_desc;

          // Check if course exists in "unique course" list
          var courseExists = false;
          var foundIndex;

          courseList.forEach(course => {
            if (course.subject_code == code) {
              foundIndex = courseList.indexOf(course);
              courseExists = true;
            }
          });

          if (courseExists) {
            // Check that student isn't already listed as being in this course.
            var studentInList = false;
            courseList[foundIndex].students.forEach(student => {
              if (student == id) studentInList = true;
            });

            // If they're not, add them.
            if (!studentInList) {
              courseList[foundIndex].students.push(id);
            }
          } else {
            // If the unique course doesn't exist, add it + the current student.
            courseList.push({
              subject_code: code,
              subject_desc: desc,
              students: [id]
            });
          }
        });
      });

      return courseList;
    }
  }
};
</script>

<style scoped>
#wrapper {
  display: flex;
}
#studentList {
  flex: 0 0 50%;
  padding: 1em;
}
#subjectList {
  flex: 1;
  padding: 1em;
  float: left;
}
input {
  width: 100%;
  margin-bottom: 1em;
}
select {
  width: 100%;
  max-height: 75vh;
}
option {
  padding: 5px;
}
pre {
  font-family: Helvetica, Arial, sans-serif;
}
</style>