<template>
  <div class='gallery-overlay invisible'>
    <span class='gallery-overlay-close'>&times;</span>
    <div class='gallery-overlay-preview'>
      <!-- <img class='gallery-overlay-image' :src='image.url_n' :alt='image.title'> -->
    </div>
  </div>
</template>
<script>
export default {
  name: 'OverlayCard',
  props: ['image'],
  methods: {
    showGallery (evt) {
      console.log(evt)
      // let gallery = document.querySelector('.gallery-overlay')
      // let closeButton = document.querySelector('.gallery-overlay-close')
      // let backgroundImage = gallery.querySelector('.gallery-overlay-image')
      // gallery.classList.remove('invisible')
    }
  }
}
</script>
<style lang='scss'>
  .invisible {
    display: none;
  }
</style>
