<template>
  <div id="app">
    <Header />
    <div class="board">
      <div class="lane" v-for="(list, index) in cards" :key="index">
        <h2 class="lane-title">{{list.title}}</h2>
        <Container
          group-name="list"
          @drag-start="handleDragStart(list.title, $event)"
          @drop="handleDrop(list.title, $event)"
          :get-child-payload="getChildPayload"
          :drop-placeholder="{className: 'placeholder'}"
        >
          <Draggable v-for="card in list.cards" :key="card.id">
            <Card>{{card.text}}</Card>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>

import Header from './components/Header';
import Card from './components/Card';
import dados from "./dados";
import {Container, Draggable} from "vue-smooth-dnd";
import collect from 'collect.js';

export default {
  name: 'App',
  components: {
    Header,
    Card,
    Container,
    Draggable
  },
  data: () => ({
    cards: collect ([
      {title: 'Backlog', cards: dados.backlog},
      {title: 'Desenvolvimento', cards: dados.dev},
      {title: 'revisão', cards: dados.rev},
      {title: 'terminou', cards: []},
    ]),
    draggingCard: {
      lane: '',
      index: -1,
      cardData: {},
    }
  }),
  methods: {
    handleDragStart(lane, dragResult){
      const { payload, isSource } = dragResult;
      if(isSource){
        this.draggingCard = {
          index: payload,
          cardData: {
            ...this.cards.where('title', lane).first().cards[payload]
          }
        }
      }
    },
    handleDrop(lane, dropResult){
      const {removedIndex, addedIndex } = dropResult;
      let list = this.cards.where('title', lane).first().cards;
      if(lane === this.draggingCard.lane && removedIndex === addedIndex){
        return;
      }
      if(list){
        if(removedIndex !== null){
          list.splice(removedIndex, 1);
        }
        if(addedIndex !== null){
          list.splice(addedIndex, 0, this.draggingCard.cardData)
        }
      }
    },
    getChildPayload(index){
      return index;
    }
  }
}
</script>
<style >
#app{
  background-color:#223333;
  height: 100vh;
}
.board{
  display: flex;
}
.lane{
  background-color: #0085ff;
  width: 24rem;
  border-radius: 0.8rem;
  box-shadow: 0.1rem .2rem 0 rgba(33, 33, 33, .1);
  height: 30rem;
  margin: 10px;
  padding: 0 0.7rem ;
}
.lane-title{
  padding: 0.8rem 0.5rem;
  margin-bottom: 0.6rem;
}
</style>


