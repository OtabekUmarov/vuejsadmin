<template>
    <v-container class="mt-4">
        <v-row>
            <h2>Maqolalar ro'yhati</h2>
            <v-spacer></v-spacer>
            <v-btn color="primary" @click="dialog = !dialog">
                <v-icon>
                    mdi-cart-plus
                </v-icon>
                <span class="ml-2">Maqola Yaratish</span>
            </v-btn>

        </v-row>
        <v-dialog v-model="dialog" max-width="700px">
            <v-card>
                <v-card-title>
                    <span class="headline" v-show="!toggle">Yangi Maqola</span>
                    <span class="headline" v-show="toggle">Maqolani tahrirlash</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-row>
                            <v-col cols="12">
                                <v-text-field label="Maqola nomi*" v-model='post.title' required></v-text-field>
                            </v-col>
                            <v-col cols="12">
                                <v-text-field label="Maqola url*" v-model='link' required></v-text-field>
                            </v-col>
                            <v-col cols="12">
                                <v-text-field label="Maqolaga qisqa ta'rif*" v-model='post.subtitle' required>
                                </v-text-field>
                            </v-col>
                            <v-col cols="12">
                                <v-text-field label="Maqola image*" v-model='post.img' required></v-text-field>
                            </v-col>
                            <v-col cols="12">
                                <vue-editor v-model='post.content'></vue-editor>
                            </v-col>
                        </v-row>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="dialog = false, post={}">
                        Yopish
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="add()" v-show="!toggle">
                        Qo'shish
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save()" v-show="toggle">
                        Saqlash
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="dialog1" width="700px">
            <v-card>
                <v-card-title>
                    <span class="headline">{{open.title}}</span>
                </v-card-title>
                <v-img class="white--text align-end mb-3" height="200px" :src="open.img">
                </v-img>
                <v-card-text class="text--primary">
                        <div>{{open.subtitle}}</div>
                    </v-card-text>
                <v-card-text v-html='open.content'></v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="green darken-1" text @click="dialog1 = false">
                        Yopish
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-row class="mt-5">
            <v-col cols="4" v-for="maqola of posts" :key="maqola.id">
                <v-card class="mx-auto">
                    <v-img class="white--text align-end" height="200px" :src="maqola.img">
                        <v-card-title>{{maqola.title}}</v-card-title>
                    </v-img>

                    <v-card-text class="text--primary">
                        <div>{{maqola.subtitle}}</div>
                    </v-card-text>

                    <v-card-actions>
                        <v-btn color="orange" text @click="open = maqola, dialog1 = true">
                            Batafsil
                        </v-btn>
                        <v-spacer></v-spacer>
                            <v-icon class="mr-2" @click="edit(maqola)">mdi-pencil</v-icon>
                            <v-icon class="mr-2" @click="del(maqola.id)">mdi-minus-circle</v-icon>
                    </v-card-actions>
                </v-card>
            </v-col>
        </v-row>
    </v-container>

</template>

<script>
    import {
        VueEditor
    } from "vue2-editor";
    import axios from 'axios'
    export default {
        data: () => ({
            dialog: false,
            dialog1: false,
            toggle: false,
            post: {
                title: '',
                url: ''
            },
            posts: [],
            delposts: [],
            open:{}
        }),
        computed: {
            link(){
                return this.post.title.split(" ").join('-').toLowerCase().replaceAll(',', '')
            }
        },
        methods: {
            save() {
                this.dialog = false
                this.toggle = false
                axios.put('http://localhost:3000/post/' + this.post.id, this.post)
                    .then(response => {
                        this.posts.forEach(cat => {
                            if (cat.id == response.data.id) {
                                cat = response.data
                            }
                        })
                    })
                this.post = {
                    title: '',
                    url: ''
                }
            },
            edit(maqola) {
                this.post = maqola
                this.toggle = true
                this.dialog = true
            },
            del(id) {

                axios.delete('http://localhost:3000/post/' + id).then(response => {
                    console.log(response)
                    this.posts = this.posts.filter(cat => {
                        return cat.id !== id
                    })

                })
            },
            add() {
                axios.post('http://localhost:3000/post', this.post)
                    .then(response => {
                        this.posts.push(response.data)
                    })
                this.post = {
                    title: '',
                    url: ''
                }
                this.dialog = false
            }
        },
        created() {
            axios.get('http://localhost:3000/post')
                .then(response => {
                    this.posts = response.data
                })
        },
        components: {
            VueEditor
        }
    }
</script>

<style>

</style>