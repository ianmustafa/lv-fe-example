<template>
  <v-container>
    <v-row>
      <v-col md="8">
        <v-data-table
          :headers="table.headers"
          :items="table.items"
          class="elevation-1">
          <template #item.gender="{ value }">
            {{ value === 'm' ? 'Male' : 'Female' }}
          </template>

          <template #item.actions="{ item }">
            <v-btn color="primary" @click="handleEdit(item)">Edit</v-btn>
          </template>
        </v-data-table>
      </v-col>

      <v-col md="4">
        <v-text-field
          v-model="payload.name"
          label="Name" />
        <v-radio-group v-model="payload.gender">
          <v-radio
            v-for="gender in options.gender"
            :key="gender.value"
            :label="gender.text"
            :value="gender.value"
          ></v-radio>
        </v-radio-group>
        <v-textarea
          v-model="payload.address"
          label="Address" />
        <v-btn color="primary" @click="handleSubmit">Submit</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    table: {
      headers: [
        {
          text: 'Name',
          sortable: false,
          value: 'name',
        },
        {
          text: 'Gender',
          sortable: false,
          value: 'gender',
        },
        {
          text: 'Address',
          sortable: false,
          value: 'address',
        },
        {
          text: 'Actions',
          sortable: false,
          value: 'actions',
        },
      ],
      items: [],
    },
    options: {
      gender: [
        { text: 'Male', value: 'm' },
        { text: 'Female', value: 'f' },
      ]
    },
    payload: {
      name: '',
      gender: '',
      address: '',
    }
  }),
  methods: {
    async handleSubmit() {
      if ('id' in this.payload) {
        const response = await this.$axios.patch(`http://localhost:8000/api/people/${this.payload.id}`, this.payload);

      } else {
        const response = await this.$axios.post('http://localhost:8000/api/people', this.payload);

        this.table.items.push(response.data);
      }


      this.payload = {
        name: '',
        gender: '',
        address: '',
      };
    },
    handleEdit(payload) {
      this.payload = payload;
    }
  },
  async mounted() {
    const response = await this.$axios.get('http://localhost:8000/api/people');
    
    this.table.items = response.data;
  }
};
</script>
