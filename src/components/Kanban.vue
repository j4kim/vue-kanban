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
      dragula(this.$refs.list)
        .on('drag', (el) => {
          el.classList.add('is-moving');
        })
        .on('drop', (el, target, source, sibling) => {
          let index = 0;
          for (index = 0; index < target.children.length; index += 1) {
            if (target.children[index].classList.contains('is-moving')) break;
          }
          this.$emit('update-block', el.dataset.cardId, target.dataset.bucketName, index);
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
