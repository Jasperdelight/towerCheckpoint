<template>
  <div class="about text-center">
    <h1>Welcome {{ account.name }}</h1>
    <img class="rounded" :src="account.picture" alt="" />
    <p>{{ account.email }}</p>

    <div class="container">
      <section class="row">
        <div v-for="ticket in myTickets" :key="ticket.id" class="col-12 danger-color card mb-2">
          
        <div class="d-flex justify-content-between">
          <router-link :to="{ name: 'Tower', params: { eventId: ticket.event.id }}">
            <img class="py-1" :src="ticket.event.coverImg" alt="">
    </router-link>
    <span class=" d-flex align-items-center">
      {{ ticket.event.name }}
    </span>
          
          <button @click="unAttendTower(ticket.id)" class="btn btn-danger my-3">Cancel</button>
        </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
import { computed, ref } from 'vue';
import { AppState } from '../AppState';
import Pop from '../utils/Pop.js';
import { logger } from '../utils/Logger.js';
import { ticketsService } from '../services/TicketsService.js';
import { accountService } from "../services/AccountService";
export default {
  setup() {
    const editable = ref({})

    return {
      account: computed(() => AppState.account),
      tickets: computed(()=> AppState.tickets),
      myTickets: computed(()=> AppState.myTickets),

      async unAttendTower(ticketId){
        try{
            // const ticketToRemove = AppState.myTickets.find(t => t.accountId == AppState.account.id)
            // const ticketId = ticketToRemove.id
            // logger.log('ticket id', ticketId)
            const wantsToDelete = await Pop.confirm('Are you sure you want to cancel your ticket to this event?')
         if (!wantsToDelete) {
          return
        }
            await ticketsService.unAttendTower(ticketId)
            
        } catch(error) {
            Pop.error(error.message);
            logger.log(error);
        }
      },
      async editAccount(){
        try{
            const formData = editable.value
            await accountService.editAccount(formData)
        } catch(error) {
            Pop.error(error.message);
            logger.log(error);
        }
      }
    }
  }
}
</script>

<style scoped>
img {
  max-width: 100px;
}

.ticket-img{
  height: 4vh;
  width: 4vh;
  border-radius: 50%;
}
</style>
