<template>
  <div>
    <div class="form-container">
      <v-form>
        <div class="form-inputs">
          <v-select @change="userTypeChange" :items="userTypes" v-model="userType" label="Search By">
          </v-select>
          <v-select v-if="showSearchBySelect" :items="personItems" v-model="selectedPerson" :label="searchBySelectLabel" :item-value="searchBySelectValue" :item-text="searchBySelectName" return-object>
          </v-select>
          <v-autocomplete v-if="userType === 'Patient'" v-model="selectedPerson" :search-input.sync="patientSearchTerm" :items="patientList" label="Search Patients" item-text="fullName">
          </v-autocomplete>
          <v-menu :nudge-right="40" transition="scale-transition" offset-y full-width min-width="290px">
            <v-text-field slot="activator" v-model="startDate" label="Start Date" readonly>
            </v-text-field>
            <v-date-picker v-model="startDate" :max="endDate">
            </v-date-picker>
          </v-menu>
          <v-menu :nudge-right="40" transition="scale-transition" offset-y full-width min-width="290px">
            <v-text-field slot="activator" v-model="endDate" label="End Date" readonly>
            </v-text-field>
            <v-date-picker v-model="endDate" :min="startDate">
            </v-date-picker>
          </v-menu>
          <v-select @change="activitySelectionChange" :items="activityTypes" v-model="selectedActivityTypes" label="Type of Activity" multiple>
            <v-list-tile slot="prepend-item" ripple @click="toggleAll">
              <v-list-tile-title>Select All</v-list-tile-title>
            </v-list-tile>
            <template slot="selection" slot-scope="{item, index}">
              <span v-if="allActivityTypesSelected && index === 0">All</span>
              <span v-else-if="!allActivityTypesSelected">{{item}}</span>
              <span v-if="!allActivityTypesSelected && index !== selectedActivityTypes.length - 1">,&nbsp;</span>
            </template>
          </v-select>
          <v-text-field v-model="commentText" label="Comment">
          </v-text-field>
          <v-checkbox
            label="Include Access Logs"
            v-model="includeAccessLogs"
            color="secondary"
          ></v-checkbox>
          <v-btn color="secondary" @click="search">
            Search
          </v-btn>
        </div>
      </v-form>
      <v-divider></v-divider>
      <v-data-table :items="auditLogResults" :headers="auditLogHeaders" hide-actions>
        <template slot="headerCell" slot-scope="props">
          <span>{{props.header.text}}</span>
        </template>
        <template slot="items" slot-scope="props">
          <td>{{ props.item.date.toISOString().substr(0, 16) }}</td>
          <td>{{ props.item.user }}</td>
          <td>{{ props.item.activity }}</td>
          <td>{{ props.item.patient }}</td>
          <td>{{ props.item.comment }}</td>
        </template>
      </v-data-table>
    </div>
  </div>
</template>

<script>
const roles = JSON.parse(window.support.getRoles()); 
const users =  JSON.parse(window.support.getUsers()); 

const patients = [];

export default {
  computed: {
    showSearchBySelect: function() {
      return this.userType === 'User' || this.userType === 'Role';
    },
    searchBySelectLabel: function() {
      return this.userType === 'User'
        ? 'Select User'
        : this.userType === 'Patient'
          ? 'Select Patient'
          : 'Select Role';
    },
    searchBySelectName: function() {
      return this.userType === 'User'
        ? 'value'
        : this.userType === 'Patient'
          ? 'fullName'
          : 'title';
    },
    searchBySelectValue: function() {
      return 'id';
    }

  },
  data: () => {
    const today = new Date();
    const oneMonthBeforeToday = new Date();
    oneMonthBeforeToday.setMonth(Math.abs(today.getMonth() - (1 % 11)));

    return {
      patientSearchTerm: '',
      startDate: oneMonthBeforeToday.toISOString().substr(0, 10),
      endDate: today.toISOString().substr(0, 10),
      userTypes: ['User', 'Patient', 'Role', 'None'],
      userType: 'User',
      activityTypes: JSON.parse(window.support.getActivityTypes()),
      selectedActivityTypes: [],
      allActivityTypesSelected: false,
      includeAccessLogs: false,
      commentText: '',
      userList: users,
      patientList: patients.map(patient => {
        patient.fullName = patient.values.fname + ' ' + patient.values.lname;
        return patient;
      }),
      roleList: roles,
      personItems: users,
      selectedPerson: '',
      auditLogHeaders: [
        {
          text: 'Date',
          sortable: false,
          value: 'date'
        },
        {
          text: 'User',
          sortable: false,
          value: 'user'
        },
        {
          text: 'Activity',
          sortable: false,
          value: 'activity'
        },
        {
          text: 'Patient',
          sortable: false,
          value: 'patient'
        },
        {
          text: 'Comment',
          sortable: false,
          value: 'comment'
        }
      ],
      auditLogResults: [
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date(),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        }
      ]
    };
  },
  methods: {
    toggleAll: function() {
      this.allActivityTypesSelected = !this.allActivityTypesSelected;
      this.$nextTick(() => {
        if (this.allActivityTypesSelected) {
          this.selectedActivityTypes = this.activityTypes;
        } else {
          this.selectedActivityTypes = [];
        }
      });
    },
    activitySelectionChange: function() {
      if (this.selectedActivityTypes.length !== this.activityTypes.length) {
        this.allActivityTypesSelected = false;
      } else {
        this.allActivityTypesSelected = true;
      }
    },
    userTypeChange: function() {
      if (this.userType === 'User') {
        this.personItems = this.userList;
      } else if (this.userType === 'Patient') {
        this.personItems = this.patientList;
      } else {
        this.personItems = this.roleList;
      }
    },
    search: function() {
      const auditSearch = {
        role: this.selectedPerson
      };
      window.auditlog.searchByRole(JSON.stringify(auditSearch));
    }

  },
  watch: {
    patientSearchTerm: function(value) {
      this.patientList = JSON.parse(window.support.patientSearch(value)).map(patient => {
        patient.fullName = patient.values.fname + ' ' + patient.values.lname;
        return patient;
      });
    }
  }
};
</script>

<style lang="scss">
.form-inputs {
  display: flex;
  margin-left: 16px;
}

.v-text-field {
  margin-right: 16px;
}

.v-form {
  margin-top: 16px;
  margin-right: 16px;
}
</style>
