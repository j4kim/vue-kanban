# vue-kanban

> A fork of [vue-kanban](https://github.com/BrockReece/vue-kanban) that makes the input data reactive and allows custom sorting.

### [Demo](https://j4kim.github.io/vue-kanban/)

### Vocabulary difference with the original project

stage -> bucket  
status -> bucket name  
block -> card

### Installation

To deploy the demo locally, run
```bash
npm install
```

And then serve it using
```bash
npm run dev
```

This is not a distributed npm package.
You can use the component by copying the `Kanban.vue` and `kanban.scss` files.

### 

### Basic Usage
Import and register the `Kanban.vue` component
```js
import Kanban from './components/Kanban'
...
components: { Kanban }
```

then simply use it.
```html
<Kanban :buckets="buckets" />
```

The `buckets` property expects an oject containing the
list names as keys and arrays of "card" objects as values.
```js
{
  buckets: {
    'on-hold': [
      {
        id: 1,
        title: 'Test',
      }
    ],
    'in-progress': [],
    'needs-review': [],
    'approved': []
    }
}
```

The `buckets` object is altered when a card is moved,
this is the major difference with the original component by @BrockReece.

### Receiving Changes
The component will emit an event when a card is moved

```html
<Kanban :buckets="buckets" @update-card="updateCard" />

<script>
...
  methods: {
    updateCard(card, bucketName) {
      console.log("card " + card.id + " was moved to bucket " + bucketName)
    },
  },
...
</script>
```

### Customize the kanban cards
You can pass a `cardComponent` property to the Kanban component

```html
<Kanban :buckets="buckets" :card-component="cardComponent" />
```

`cardComponent` contains a Vue component object. This component will receive a `card` property, containing the card object.
```js
{
  cardComponent: { 
    template: `
      <div>
        Custom card
        <i>{{ card.title }}</i>
      </div>
    `,
    props: ['card']
  }
}
```


### Sort

When a card is moved from a bucket to another, the order of this new bucket is stored in the card objects, in an `order` property. Then, the original `buckets` object is altered to be consistent with the real order of the cards in the buckets.

You can pass a [comparison function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Description) in the `sorter` prop to the Kanban component to change this behavior.
For example, here we will reorder the card by `id` descending when a card is moved in a bucket :
```html
<Kanban :buckets="buckets" :sorter="(cardA, cardB) => cardB.id - cardA.id" />
```

