<template>
    <div class="container">
        <Navbar />
        <div class="content" v-if="!loading">
            <div class="send-ticket" v-if="currentUser.type == 1">
                <h1 class="text-center m-5">Wyślij zgłoszenie</h1>
                <form @submit.prevent="submit_ticket" class="col-7 mx-auto">
                    <div class="form-group m-2">
                        <input class="form-control" type="text" name="title" v-model="ticket.title" placeholder="Tytuł" maxlength="255" required>
                        <br>
                        <textarea class="form-control" name="description" v-model="ticket.description" placeholder="Opis" required></textarea>
                    </div>
                    <button type="submit" class="btn btn-primary m-2 d-block">Wyślij</button>
                </form>
            </div>
            <h1 class="text-center m-5">Przypisane zgłoszenia</h1>
            <div class="table-responsive-lg">
                <table class="table text-center">
                    <thead>
                        <tr>
                            <th>Zgłoszone przez</th>
                            <th>Przydzielone do</th>
                            <th>Tytuł</th>
                            <th>Status</th>
                            <th>Aktywność</th>
                            <th>Akcja</th>
                        </tr>
                    </thead>
                    <tbody class="align-middle">
                        <tr v-for="ticket in tickets" :key="ticket.id">
                            <td class="col-md-2">{{ getUserById(ticket.userId).username }}</td>
                            <td class="col-md-2">{{ getUserById(ticket.technicianId).username }}</td>
                            <td class="col-md-4">{{ ticket.title }}</td>
                            <td class="col-md-1">
                                <span v-if="ticket.status == 2" class="badge bg-success">{{ getTicketStatus(ticket.status).toUpperCase() }}</span>
                                <span v-else-if="ticket.status == 1" class="badge bg-info">{{ getTicketStatus(ticket.status).toUpperCase() }}</span>
                                <span v-else class="badge bg-danger">{{ getTicketStatus(ticket.status).toUpperCase() }}</span>
                            </td>
                            <td class="col-md-1"><span class="badge badge-primary">{{ ms(Date.now() - (ticket.updated || ticket.date)) }} temu</span></td>
                            <td class="col-md-2">
                                <router-link :to="'/panel/' + ticket.id">Sprawdź zgłoszenie</router-link>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <Loader v-else class="loader" color="#1470bb" :width="400" :height="5" sizeUnit="px" />
        <Footer />
    </div>
</template>

<script>
import {BarLoader} from "@saeris/vue-spinners"
import ms from "ms"

import Navbar from "../components/Navbar.vue"
import Footer from "../components/Footer.vue"

import getTicketStatus from '../../helpers/getTicketStatus.js'

export default {
    name: "Panel",
    components: {
        Loader: BarLoader,
        Navbar,
        Footer
    },
    data() {
        return {
            currentUser: null,
            loading: true,
            tickets: [],
            users: [],
            ms,
            getTicketStatus,
            ticket: {
                title: null,
                description: null
            }
        }
    },
    created: async function() {
        await fetch('/api/user/info').then(res => res.json()).then(data => this.currentUser = data.user);
        await fetch('/api/tickets').then(res => res.json()).then(data => this.tickets = data);
        await fetch('/api/users').then(res => res.json()).then(data => this.users = data);
        this.loading = false;
    },
    methods: {
        getUserById(id) {
            return this.users.find(user => user.id == id);
        },
        submit_ticket: async function() {
            if (this.ticket.title.length <= 255 && this.ticket.description) {
                const title = this.ticket.title;
                const description = this.ticket.description;

                const res = await fetch("/api/tickets/send", {
                    method: "POST",
                    headers: {"Content-Type": "application/json"},
                    body: JSON.stringify({ title, description })
                });
                
                const body = await res.json();
                if (body.success) this.$router.push(`/panel/${body.ticket.id}`);
            }
        }
    }
}
</script>