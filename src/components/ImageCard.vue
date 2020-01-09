<template>
  <li class="image-card" @click="showGallery">
    <img class="image-card__image" :src="image.url_n" :alt="image.title">
    <div class="image-card__body">
      <p v-if="image.title" class="image-card__title">{{image.title}}</p>
      <p v-else class="image-title">No Title Found</p>
      <p class="image-card__owner">By {{image.ownername}}</p>
      <section class="image-card__wrapper">
        <p class="image-card__date">{{image.datetaken | moment}}</p>
        <p class="image-card__views">Views: {{image.views}}</p>
      </section>
    </div>
  </li>
</template>
<script>
import moment from 'moment'
export default {
  name: 'ImageCard',
  props: [ 'image' ],
  filters: {
    moment (date) {
      return moment(date).format('Do MMMM YYYY')
    }
  },
  methods: {
    showGallery (evt) {
      console.log(evt)
    }
  }

}
</script>
<style lang="scss">
.image-card {
  width: calc(33% - 1rem);
  margin: .5rem;
  border-radius: 4px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, .15);
  background: white;
  @media only screen and (max-width: 799px) {
    width: calc(50% - 1rem);
  }
  @media only screen and (max-width: 549px) {
    width: 100%;
    margin: .5rem 0;
  }
  @media  (min-width: 992px) {
    width: calc(25% - 1rem);
  }
}
.image-card__image {
  border-radius: 5px 5px 0 0;
  width: 100%;
  height: 200px;
  object-fit: cover;
}
.image-card__body {
  padding: .5rem 1rem 1rem;
}
.image-card__title {
  font-weight: bold;
  margin: 0;
}
.image-card__owner {
  margin-top: 0;
  font-size: .8rem;
}
.image-card__title,
.image-card__owner {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
.image-card__wrapper {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.image-card__date,
.image-card__views {
  margin-bottom: 0;
  font-size: .8rem;
}
</style>
