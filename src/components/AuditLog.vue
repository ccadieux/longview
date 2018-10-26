<template>
  <div>
    <div class="form-container">
      <v-form>
        <div class="form-inputs">
          <v-select @change="userTypeChange" :items="userTypes" v-model="userType" label="Search By">
          </v-select>
          <v-select v-if="showSearchBySelect" :items="personItems" v-model="selectedPerson" :label="searchBySelectLabel">
          </v-select>
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
    </div>
  </div>
</template>

<script>
export default {
  computed: {
    showSearchBySelect: function() {
      return (
        this.userType === 'User' ||
        this.userType === 'Patient' ||
        this.userType === 'Role'
      );
    },
    searchBySelectLabel: function() {
      return this.userType === 'User'
        ? 'Select User'
        : this.userType === 'Patient'
          ? 'Select Patient'
          : 'Select Role';
    }
  },
  data: () => {
    const today = new Date();
    const oneMonthBeforeToday = new Date();
    oneMonthBeforeToday.setMonth(Math.abs(today.getMonth() - (1 % 11)));
    const userList = ['User 1', 'User 2'];

    return {
      startDate: oneMonthBeforeToday.toISOString().substr(0, 10),
      endDate: today.toISOString().substr(0, 10),
      userTypes: ['User', 'Patient', 'Role', 'None'],
      userType: 'User',
      activityTypes: ['Activity 1', 'Activity 2', 'Activity 3'],
      selectedActivityTypes: [],
      allActivityTypesSelected: false,
      includeAccessLogs: false,
      commentText: '',
      userList: userList,
      patientList: ['Patient 1', 'Patient 2'],
      roleList: ['Role 1', 'Role 2'],
      personItems: userList,
      selectedPerson: '',
      auditLogResults: [
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
          user: 'User',
          activity: 'Activity',
          patient: 'Patient',
          comment: 'Comment'
        },
        {
          date: new Date().toISOString().substr(0, 10),
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
