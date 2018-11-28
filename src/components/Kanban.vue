<template>
  <div class="drag-container">
    <ul class="drag-list">
      <li
        v-for="(cards, bucketName) in buckets"
        class="drag-column"
        :class="'drag-column-' + bucketName"
        :key="bucketName"
      >
        <span class="drag-column-header">
          <slot :name="bucketName">
            <h2>{{ bucketName }}</h2>
          </slot>
        </span>
        <div class="drag-options"></div>
        <ul class="drag-inner-list" ref="list" :data-bucket-name="bucketName">
          <li class="drag-item" :class="card.class" v-for="card in cards" :data-card-id="card.id" :key="card.id">
            <slot :name="card.id">
              <strong>{{ card.title }}</strong>
              <div>{{ card.id }}</div>
            </slot>
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script>
  import dragula from 'dragula';

  export default {
    name: 'KanbanBoard',

    props: {
      buckets: Object,
      sorter: {
        type: Function,
        default: function(cardA, cardB){
          // return negative value: A before B
          return cardA.order - cardB.order
        }
      }
    },

    computed: {
      allCards() {
        return Object.values(this.buckets)
          .reduce((arr1, arr2) => {
            return arr1.concat(arr2)
          })
      }
    },

    methods: {
      getCard(id) {
        return this.allCards.find(card => card.id == id)
      }
    },

    mounted() {
      var vue = this
      dragula(this.$refs.list)
        .on('drag', (el) => {
          el.classList.add('is-moving');
        })
        .on('drop', (el, targetUl, sourceUl, sibling) => {
          var oldBucketName = sourceUl.dataset.bucketName
          var oldBucket = vue.buckets[oldBucketName]
          var newBucketName = targetUl.dataset.bucketName
          var newBucket = vue.buckets[newBucketName]

          // get card object
          var card = vue.getCard(el.dataset.cardId)

          // iterate over card elements in bucket to assign corresponding card object an order
          var i = 0
          Array.from(targetUl.children).forEach(cardLi => {
            vue.getCard(cardLi.dataset.cardId).order = i++
          })
          
          if(oldBucketName !== newBucketName) {
            // remove from bucket
            oldBucket = oldBucket.filter(c => c.id != card.id)

            // move card into new bucket and sort it
            newBucket.push(card)
            newBucket.sort(vue.sorter)

            // store updated buckets
            vue.buckets[oldBucketName] = oldBucket
            vue.buckets[newBucketName] = newBucket
          }

          vue.$emit('update-card', card, newBucketName)

          vue.$set(card, 'class', 'is-moved')

          setTimeout(() => {
            vue.$set(card, 'class', '')
          }, 600)

        })
        .on('dragend', (el) => {
          el.classList.remove('is-moving')
        });
    },
  };
</script>
