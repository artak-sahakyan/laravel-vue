<template>
    <div>
        Page {{page}} of {{totalPages}}
        <v-simple-table>
            <template v-slot:default>
                <thead>

                <tr>
                    <th v-for="(field, fieldName) in fields" @click="setSort(fieldName)">{{field.label}}</th>
                </tr>

                <tr>
                    <th v-for="(field, fieldName) in fields">
                        <v-select @change="filterProperties"
                            v-if="field.options" v-model="filterFields[fieldName]"
                            :items="getSelectOptions(field.options)"
                        ></v-select>
                        <v-text-field v-else v-model="filterFields[fieldName]" @input="filterProperties"></v-text-field>
                    </th>
                </tr>



                </thead>

                <tbody>
                <tr v-for="property in properties" :key="property.id">
                    <td>{{property.title}}</td>
                    <td>{{property.description}}</td>
                    <td>{{property.bedroom}}</td>
                    <td>{{property.bathroom}}</td>
                    <td>{{property.property_type.name}}</td>
                    <td>{{property.status.name}}</td>
                    <td v-if="property.for_sale">YES</td>
                    <td v-else>No</td>
                    <td v-if="property.for_rent">YES</td>
                    <td v-else>No</td>
                    <td>{{property.project.name}}</td>
                    <td>{{property.region.country.name}}</td>


                </tr>
                </tbody>
            </template>
        </v-simple-table>
        <div class="text-center">
            <div class="my-2">
                <v-btn v-if="prevPageUrl" depressed small color="primary" @click="getProperties(prevPageUrl)">Previous Page</v-btn>
                    <v-btn v-else depressed small disabled>Previous Page</v-btn>

                <v-btn v-if="nextPageUrl" depressed small color="primary" @click="getProperties(nextPageUrl)">Next Page</v-btn>
                    <v-btn v-else depressed small disabled>Next Page</v-btn>
            </div>

            <div class="my-2">
                Page {{page}} of {{totalPages}}
            </div>
        </div>
    </div>

</template>

<script>
    import axios from 'axios'
    export default {
        data () {
            return {
                url:'http://laravel-api.loc',
                nextPageUrl: '',
                prevPageUrl: '',
                properties: [],
                page: 1,
                totalPages: 1,
                sortField: 'id',
                sortType: 'asc',
                filterFields: {},
                filterOptions: {},
                fields: {}
            }
        },

        methods: {
            getSelectOptions(data) {
                let items = [];

                for(let key in data) {
                    items.push({
                        text: key,
                        value: data[key]
                    })
                }

                return items;
            },
            setSort(field) {
                if (this.sortField == field) {
                    this.sortType = this.sortType == 'ASC' ? 'DESC' : 'ASC';
                } else {
                    this.sortField = field;
                    this.sortType = 'ASC';
                }
                this.getProperties();
            },

            filterProperties(){
                this.prevPageUrl = '';
                this.nextPageUrl = '';
                this.getProperties();
            },

            getProperties(url) {

                if(!url) {
                    url = this.url+'/api/properties';
                }
                let requestData = {
                    filterFields: this.filterFields,
                    sortField: this.sortField,
                    sortType: this.sortType,
                }


                axios
                    .post(url, requestData, {
                        headers: {
                            'Content-Type': 'application/json'
                        }
                    })
                    .then(response => {
                        let result = response.data;
                        this.properties = result.data;
                        this.nextPageUrl = result.next_page_url;
                        this.prevPageUrl = result.prev_page_url;
                        this.page = result.current_page;
                        this.totalPages = result.last_page;
                    })
                    .catch(error => {
                        console.log(error)
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
                        this.fields = response.data;
                        console.log(this.fields)
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
