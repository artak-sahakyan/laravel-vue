<template>
    <v-simple-table>
        <template v-slot:default>
            <thead>

            <tr>
                <th v-for="(field, fieldName) in fields">
                    <v-text-field v-model="filterFields[fieldName]" @input="getProperties"></v-text-field>
                </th>
            </tr>

            <tr>
                <th v-for="(field, fieldName) in fields" @click="setSort(fieldName)">{{field}}</th>
            </tr>

            </thead>

            <tbody>
            <tr v-for="property in properties" :key="property.title">
                <td v-for="(field, fieldName) in fields">{{ property[fieldName] }}</td>
            </tr>
            </tbody>
        </template>
    </v-simple-table>
</template>

<script>
    import axios from 'axios'
    export default {
        data () {
            return {
                url:'http://laravel-api.loc',
                properties: [],
                page: 1,
                sortField: null,
                sortType: null,
                filterFields: {},
                fields: {
                    title: 'Title',
                    description: 'Description',
                    bedroom: 'Bedroom',
                    bathroom: 'bathroom',
                    propertyType: 'Property Type',
                    status: 'Status',
                    forSale: 'for sale',
                    forRent: 'forRent',
                    projectName: 'projectName',
                    country: 'country',
                }
            }
        },

        methods: {
            setSort(field) {
                if (this.sortField == field) {
                    this.sortType = this.sortType == 'ASC' ? 'DESC' : 'ASC';
                } else {
                    this.sortField = field;
                    this.sortType = 'ASC';
                }
                this.getProperties();
            },

            getProperties() {

                let requestData = {
                    filterFields: this.filterFields,
                    sortField: this.sortField,
                    sortType: this.sortType,
                    page: this.page
                }


                axios
                    .post(this.url+'/api/properties', requestData, {
                        headers: {
                            'Content-Type': 'application/json'
                        }
                    })
                    .then(response => {
                        console.log(response);
                    })
                    .catch(error => {
                        console.log(error)
                    })


                console.log(requestData);
                setTimeout(() => {
                    this.properties = [
                        {
                            title: 'sadasdss',
                            description: 'descasda',
                            bedroom: 3,
                            bathroom: 5,
                            propertyType: 'Condo',
                            status: 'sadasd',
                            forSale: 'sSsssd',
                            forRent: 'sSsssd',
                            projectName: 'ssdfsd ds dsf Ssssd',
                            country: 'Armenia',

                        },
                        {
                            title: 'sadasd',
                            description: 'descasda',
                            bedroom: 3,
                            bathroom: 5,
                            propertyType: 'Condo',
                            status: 'sadasd',
                            forSale: 'sSsssd',
                            forRent: 'sSsssd',
                            projectName: 'ssdfsd ds dsf Ssssd',
                            country: 'Armenia',

                        },
                        {
                            title: 'sadggasd',
                            description: 'descasda',
                            bedroom: 3,
                            bathroom: 5,
                            propertyType: 'Condo',
                            status: 'sadasd',
                            forSale: 'sSsssd',
                            forRent: 'sSsssd',
                            projectName: 'ssdfsd ds dsf Ssssd',
                            country: 'Armenia',

                        },
                    ]
                })
            },
            getFilterOptions() {
                axios
                    .get(this.url + '/api/filter-options', {
                        headers: {
                            'Content-Type': 'application/json'
                        }
                    })
                    .then(response => {

                    })
                    .catch(error => {
                        console.log(error)
                    })

            }
        },

        created() {

            this.getFilterOptions();
            this.getProperties();
        }

    }
</script>
