<template>
  <div id="app">
    <section class="section">
      <h4>
        usage example of <a href="https://github.com/j4kim/vue-kanban">J4kim's fork</a> of <a href="https://github.com/BrockReece/vue-kanban">BrockReece' vue-kanban</a>
      </h4>
    </section>
    <Kanban :buckets="buckets" @update-card="updateCard" :card-component="cardComponent">
      <div slot="on-hold" >
        <h2>
          On hold
          <a style="cursor:pointer" @click="addCard">+</a>
        </h2>
      </div>
    </Kanban>
  </div>
</template>

<script>
import faker from 'faker';
import { debounce, random } from 'lodash';
import Kanban from './components/Kanban';
import cardComponent from './components/Card';

var i = 0

export default {
  name: 'app',
  components: {
    Kanban,
  },
  data() {
    return {
      buckets: {
        "on-hold": [],
        "in-progress": [],
        "needs-review": [],
        "approved": []
      },
      cardComponent
    }
  },
  created() {
    for (i; i <= 10; i += 1) {
      let bucketName = Object.keys(this.buckets)[random(3)]
      this.buckets[bucketName].push({
        id: i,
        title: faker.company.bs(),
      });
    }
  },

  methods: {
    addCard() {
      this.buckets['on-hold'].push({
        id: i++,
        title: faker.company.bs(),
      });
    },
    updateCard: console.log
  },
};
</script>

<style lang="scss">
  @import './assets/kanban.scss';

  $on-hold: #FB7D44;
  $in-progress: #2A92BF;
  $needs-review: #F4CE46;
  $approved: #00B961;

  * {
    box-sizing: border-box;
  }

  body {
    background: #33363D;
    color: white;
    font-family: 'Lato';
    font-weight: 300;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
  }

  .drag-column {
    .drag-column-header > div {
      width: 100%;
      h2 > a {
        float: right;
      }
    }

    &-on-hold {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $on-hold;
      }
    }

    &-in-progress {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $in-progress;
      }
    }

    &-needs-review {
      .drag-column-header,
      .is-moved,
      .drag-options{
        background: $needs-review;
      }
    }

    &-approved {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $approved;
      }
    }
  }

  .section {
    padding: 20px;
    text-align: center;

    a {
      color: white;
      text-decoration: none;
      font-weight: 300;
    }

    h4 {
      font-weight: 400;
      a {
        font-weight: 600;
      }
    }
  }
</style>
