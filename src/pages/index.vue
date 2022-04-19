<script setup lang="ts">
import { useUserStore } from '~/stores/user'
import axios from "axios";
import 'animate.css';

const api = 'https://api.learntoaster.com/api';

const user = useUserStore()
const name = ref(user.savedName)

const router = useRouter()

const randomfacts = ref([]);
const getFacts = () => {
  randomfacts.value = [];
  axios.get(api+'/facts/landing').then(response => {
    response.data.forEach(function(fact: any){
      randomfacts.value.push(fact);
    });
  });
}
getFacts();

const open = (fact : any) => {

  fact.opened = true;
  randomfacts.value.forEach(function(otherfact){
    if(otherfact!=fact)otherfact.fade = true;
  });
  Vue.$forceUpdate();

}

router.push(`/wip/`)

const { t } = useI18n()
</script>

<template>
  <div class="scroller">

    <div class="landing-page">
      <div class="card-wrapper">

        <div v-for="fact in randomfacts"
             p="x4 y10"
             w="370px"
             h="250px"
             :class="'m-auto relative animate__animated ' + (fact.fade ? 'animate__zoomOut' : '') + (fact.opened ? 'animate__flipInY' : '')"
             text="center"
             bg="transparent"
             border="~ rounded gray-200 dark:gray-700"
             outline="none active:none"
             :style="!fact.opened ? 'cursor:pointer' : ''"
             @click="open(fact)"
        >
          <div v-if="fact.opened" class="">
            <p text-xl><strong>{{ fact.fact }}</strong></p>
            <p><small>({{ fact.additional }})</small></p>
            <div py-3 />

            <p :style="fact.resources == 'n/a' ? 'opacity:0' : ''">{{ fact.resources }}</p>

            <div py-3 />

<!--            <p class="bottom"><small>{{ fact.category.name }} → {{ fact.subcategory.name }} → {{ fact.subject.name }}</small></p>-->
          </div>

          <div v-else >

            <div py-4 />

            <p text-xl><strong>{{ fact.subject.name }}</strong></p>
            <p><small>{{ fact.subcategory.name }}</small></p>

            <p><small>{{ fact.category.name }}</small></p>


          </div>

          <div class="round-buttons" v-if="fact.opened">
            <button v-if="!fact.liked"
                btn m-8 text-xl
                :class="'btn-round animate__animated '+(fact.disliked ? 'animate__fadeInDown ' : '' )"
                @click="fact.disliked=true"
                :style="fact.disliked ? 'color:red;background-color:transparent;font-size:44px' : 'color:white'"
            >
              ↓
            </button>


            <button v-if="!fact.disliked"
                btn m-8 text-xl
                :class="'btn-round animate__animated '+(fact.liked ? 'animate__jackInTheBox' : '' )"
                @click="fact.liked=true"
                :style="fact.liked ? 'color:green;background-color:transparent;font-size:44px' : 'color:white'"
            >
              ♥
            </button>

          </div>
        </div>
      </div>



      <div class="buttons animate__animated animate__delay-3s animate__fadeIn" v-if="randomfacts.find(function(fact){return fact.opened; }) ">

        <button
            btn m-8 text-xl
            class="btn-primary"
            @click=""
        >
          {{ t('button.iknewthis') }}
        </button>

        <button
            btn m-8 text-xl
            class="btn-secondary"
            @click="getFacts"
        >
          {{ t('button.next') }}
        </button>
      </div>
    </div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
