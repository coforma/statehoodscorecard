<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div>{{ memberChamber }} | {{ memberState }} | {{ memberFirstName }} {{ memberLastName }}</div>
    <br>

    <div v-for="member in foundMember" :key="member.first_name">
      <h1>{{ member.short_title }} {{ member.first_name }} {{ member.last_name }} ({{ member.party }})</h1>
      <p>Contact form: <a :href="member.contact_form"> {{ member.contact_form }}</a></p>
      <p>Phone number: {{ member.phone }}</p>
    </div>

    <br>

    <div v-for="member in orderedMembers" :key="member.id">

      <strong>{{ member.state }}</strong> <router-link
      :to="{ page: 'Member', params: { chamber: 'senate', state: member.state, first_name: member.first_name, last_name: member.last_name }}"
      >
         {{ member.first_name }} {{ member.last_name }}
      </router-link>
    </div>


  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Member',

  data () {
    return {
      members: '',
      foundMember: '',
      loading: false,
      memberFirstName: '',
      memberLastName: '',
      memberChamber: '',
      memberState: ''
    }
  },
  beforeMount () {
    this.getInfoFromPath()
    this.findMember()
  },
  methods: {
    findMember () {
      this.loading = true
      axios
        .get('https://api.propublica.org/congress/v1/116/senate/members.json?in_office=true', {
          headers: {
            'X-API-Key': 'rUfH2zXTrNFov9fc6hUqvCqLyvYQXei5NSjf0W9U'
          }
        })
        .then((response)  =>  {
          this.loading = false;
          this.members = response.data.results[0].members;
          this.foundMember = this.members.filter(m => m.first_name == this.memberFirstName && m.last_name == this.memberLastName && m.state == this.memberState);
        },
        (error)  =>  {
          this.loading = false;
        })
    },
    getInfoFromPath () {
      this.memberChamber = this.$route.params.chamber
      this.memberState = this.$route.params.state
      this.memberFirstName = this.$route.params.first_name
      this.memberLastName = this.$route.params.last_name
    }
  },
  computed: {
    orderedMembers: function () {
      return _.orderBy(this.members, 'state')
    }
  }
}
</script>
