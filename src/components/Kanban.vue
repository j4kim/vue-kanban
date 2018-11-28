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
          <li class="drag-item" v-for="card in cards" :data-card-id="card.id" :key="card.id">
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

    props: ['buckets'],

    mounted() {
      var self = this
      dragula(this.$refs.list)
        .on('drag', (el) => {
          el.classList.add('is-moving');
        })
        .on('drop', (el, target, source, sibling) => {
          var cardId = el.dataset.cardId
          var oldBucketName = source.dataset.bucketName
          var oldBucket = self.buckets[source.dataset.bucketName]
          var newBucketName = target.dataset.bucketName

          // get card object
          var card = oldBucket.find(card => card.id == cardId)

          setTimeout(() => {
            // remove from bucket
            self.buckets[oldBucketName] = oldBucket.filter(card => card.id != cardId)

            // move it to new bucket
            self.buckets[newBucketName].push(card)

            this.$emit('update-card', card, newBucketName);
          }, 700)
        })
        .on('dragend', (el) => {
          el.classList.remove('is-moving');

          window.setTimeout(() => {
            el.classList.add('is-moved');
            window.setTimeout(() => {
              el.classList.remove('is-moved');
            }, 600);
          }, 100);
        });
    },
  };
</script>
