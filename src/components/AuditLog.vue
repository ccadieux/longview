<template>
  <div>
    <div class="form-container">
      <v-form>
        <div class="form-inputs">
          <v-select @change="userTypeChange" :items="userTypes" v-model="userType" label="Search By">
          </v-select>
          <v-select v-if="showSearchBySelect" :items="personItems" v-model="selectedPerson" :label="searchBySelectLabel" :item-text="searchBySelectName">
          </v-select>
          <v-autocomplete v-if="userType === 'Patient'" :search-input.sync="patientSearchTerm" :items="patientList" label="Search Patients" item-text="fullName">
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
          <v-btn color="secondary">
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
const roles = [
  {
    isDefault: true,
    role_id: -10,
    title: 'Power User',
    inactive: false,
    accessMap: {
      'Traffic Manager': 'READ_WRITE',
      'EMR - Chronic Conditions': 'READ_WRITE',
      'EMR - Medical History': 'READ_WRITE',
      Documents: 'READ_WRITE',
      'Patient Diagnostics': 'READ_WRITE',
      'Reporting - Scheduling': 'READ_WRITE',
      'EMR - Virtual Chart': 'READ_WRITE',
      'Billing - General': 'READ_WRITE',
      'EMR - Day Sheet': 'READ_WRITE',
      'EMR - Labs': 'READ_WRITE',
      'Reporting - Clinical': 'READ_WRITE',
      'EMR - Forms': 'READ_WRITE',
      'Patient Demographics': 'READ_WRITE',
      'Wait List': 'READ_WRITE',
      Messaging: 'READ_WRITE',
      'EMR - Clinical Notes': 'READ_WRITE',
      'Billing - Private \u0026 3rd Party': 'READ_WRITE',
      Scheduling: 'READ_WRITE',
      'EMR - Medications \u0026 Allergies': 'READ_WRITE',
      'Reporting - Billing': 'READ_WRITE'
    }
  },
  {
    isDefault: true,
    role_id: -9,
    title: 'Reception',
    inactive: false,
    accessMap: {
      'Traffic Manager': 'READ_WRITE',
      'EMR - Chronic Conditions': 'NO_ACCESS',
      'EMR - Medical History': 'NO_ACCESS',
      Documents: 'NO_ACCESS',
      'Patient Diagnostics': 'NO_ACCESS',
      'Reporting - Scheduling': 'READ_WRITE',
      'EMR - Virtual Chart': 'NO_ACCESS',
      'Billing - General': 'NO_ACCESS',
      'EMR - Day Sheet': 'NO_ACCESS',
      'EMR - Labs': 'NO_ACCESS',
      'Reporting - Clinical': 'NO_ACCESS',
      'EMR - Forms': 'NO_ACCESS',
      'Patient Demographics': 'READ_WRITE',
      'Wait List': 'READ_WRITE',
      Messaging: 'READ_WRITE',
      'EMR - Clinical Notes': 'NO_ACCESS',
      'Billing - Private \u0026 3rd Party': 'NO_ACCESS',
      Scheduling: 'READ_WRITE',
      'EMR - Medications \u0026 Allergies': 'NO_ACCESS',
      'Reporting - Billing': 'NO_ACCESS'
    }
  },
  {
    isDefault: true,
    role_id: -8,
    title: 'Billing',
    inactive: false,
    accessMap: {
      'Traffic Manager': 'READ_ONLY',
      'EMR - Chronic Conditions': 'NO_ACCESS',
      'EMR - Medical History': 'NO_ACCESS',
      Documents: 'NO_ACCESS',
      'Patient Diagnostics': 'NO_ACCESS',
      'Reporting - Scheduling': 'READ_WRITE',
      'EMR - Virtual Chart': 'NO_ACCESS',
      'Billing - General': 'READ_WRITE',
      'EMR - Day Sheet': 'NO_ACCESS',
      'EMR - Labs': 'NO_ACCESS',
      'Reporting - Clinical': 'NO_ACCESS',
      'EMR - Forms': 'NO_ACCESS',
      'Patient Demographics': 'READ_WRITE',
      'Wait List': 'READ_ONLY',
      Messaging: 'READ_WRITE',
      'EMR - Clinical Notes': 'NO_ACCESS',
      'Billing - Private \u0026 3rd Party': 'READ_WRITE',
      Scheduling: 'READ_ONLY',
      'EMR - Medications \u0026 Allergies': 'NO_ACCESS',
      'Reporting - Billing': 'READ_WRITE'
    }
  },
  {
    isDefault: true,
    role_id: -7,
    title: 'Nurse',
    inactive: false,
    accessMap: {
      'Traffic Manager': 'READ_WRITE',
      'EMR - Chronic Conditions': 'NO_ACCESS',
      'EMR - Medical History': 'NO_ACCESS',
      Documents: 'NO_ACCESS',
      'Patient Diagnostics': 'NO_ACCESS',
      'Reporting - Scheduling': 'READ_WRITE',
      'EMR - Virtual Chart': 'NO_ACCESS',
      'Billing - General': 'NO_ACCESS',
      'EMR - Day Sheet': 'NO_ACCESS',
      'EMR - Labs': 'NO_ACCESS',
      'Reporting - Clinical': 'NO_ACCESS',
      'EMR - Forms': 'NO_ACCESS',
      'Patient Demographics': 'READ_WRITE',
      'Wait List': 'READ_WRITE',
      Messaging: 'READ_WRITE',
      'EMR - Clinical Notes': 'NO_ACCESS',
      'Billing - Private \u0026 3rd Party': 'NO_ACCESS',
      Scheduling: 'READ_WRITE',
      'EMR - Medications \u0026 Allergies': 'READ_ONLY',
      'Reporting - Billing': 'NO_ACCESS'
    }
  },
  {
    isDefault: true,
    role_id: -5,
    title: 'Administrator',
    inactive: false,
    accessMap: {
      'Traffic Manager': 'READ_WRITE',
      'EMR - Chronic Conditions': 'NO_ACCESS',
      'EMR - Medical History': 'NO_ACCESS',
      Documents: 'NO_ACCESS',
      'Patient Diagnostics': 'NO_ACCESS',
      'Reporting - Scheduling': 'NO_ACCESS',
      'EMR - Virtual Chart': 'NO_ACCESS',
      'Billing - General': 'NO_ACCESS',
      'EMR - Day Sheet': 'NO_ACCESS',
      'EMR - Labs': 'NO_ACCESS',
      'Reporting - Clinical': 'NO_ACCESS',
      'EMR - Forms': 'NO_ACCESS',
      'Patient Demographics': 'READ_WRITE',
      'Wait List': 'READ_WRITE',
      Messaging: 'READ_WRITE',
      'EMR - Clinical Notes': 'NO_ACCESS',
      'Billing - Private \u0026 3rd Party': 'NO_ACCESS',
      Scheduling: 'READ_WRITE',
      'EMR - Medications \u0026 Allergies': 'NO_ACCESS',
      'Reporting - Billing': 'NO_ACCESS'
    }
  },
  {
    isDefault: true,
    role_id: -1,
    title: 'Physician',
    inactive: false,
    accessMap: {
      'Traffic Manager': 'READ_WRITE',
      'EMR - Chronic Conditions': 'READ_WRITE',
      'EMR - Medical History': 'READ_WRITE',
      Documents: 'READ_WRITE',
      'Patient Diagnostics': 'READ_WRITE',
      'Reporting - Scheduling': 'READ_WRITE',
      'EMR - Virtual Chart': 'READ_WRITE',
      'Billing - General': 'READ_WRITE',
      'EMR - Day Sheet': 'READ_WRITE',
      'EMR - Labs': 'READ_WRITE',
      'Reporting - Clinical': 'READ_WRITE',
      'EMR - Forms': 'READ_WRITE',
      'Patient Demographics': 'READ_WRITE',
      'Wait List': 'READ_WRITE',
      Messaging: 'READ_WRITE',
      'EMR - Clinical Notes': 'READ_WRITE',
      'Billing - Private \u0026 3rd Party': 'READ_WRITE',
      Scheduling: 'READ_WRITE',
      'EMR - Medications \u0026 Allergies': 'READ_WRITE',
      'Reporting - Billing': 'READ_WRITE'
    }
  }
];

const users = [
  {
    toString: 'DavidDoctor',
    toolTip: 'DavidDoctor',
    value: 'DavidDoctor',
    style: 1
  },
  {
    toString: 'MandyMedic',
    toolTip: 'MandyMedic',
    value: 'MandyMedic',
    style: 1
  },
  {
    toString: 'RitaReception',
    toolTip: 'RitaReception',
    value: 'RitaReception',
    style: 1
  }
];

const patients = [
  {
    type: 'Patient',
    values: {
      fname: 'Sandi',
      status_description: 'Active',
      address: '1723 Beach Blvd.',
      birthdate: '1958-Nov-11',
      city: 'Suntown',
      sex: 'F',
      status_color: '-16777216',
      mname: '',
      alias_type: '',
      file_number: '22-12345',
      lname: 'Beach',
      patient_id: '2',
      carecard: '1234231231',
      is_alias: '0',
      provider_id: '',
      provider_name: '--None--',
      email: ''
    },
    id: 2,
    style: -1
  }
];

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
      activityTypes: ['Activity 1', 'Activity 2', 'Activity 3'],
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
    }
  },
  watch: {
    patientSearchTerm: value => {
      console.log(value);
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
