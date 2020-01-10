<template>
  <div>
    <nav class='navbar'>
      <form class='searchbar'>
        <label>
          <span class='screen-reader-only'>Search:</span>
          <input
            v-model='tag'
            placeholder='Поиск фото'
            type='text'
            class='searchbar-input'
          />
        </label>
        <button
          type='submit'
          class='btn btn--green btn--go'
          @click.prevent='search'
        >
          Go
        </button>
      </form>
    </nav>
    <div class='container'>
      <p v-if='loading' class='text-centered'>
        <spinner size='medium' message='Loading...' />
      </p>
      <ul v-else class='card-grid' @click.prevent="showGallery">
        <image-card v-for='image in cleanImages' :key='image.id' :image='image' />
      </ul>
    </div>
    <jw-pagination :items="cleanImages" @changePage="onChangePage"></jw-pagination>
    <overlay-card/>
  </div>
</template>

<script>
import config from '../../config'
import axios from 'axios'
import Spinner from 'vue-simple-spinner'
import ImageCard from '@/components/ImageCard'
import OverlayCard from '@/components/OverlayCard'
import JwPagination from 'jw-vue-pagination'
// Vue.component('jw-pagination', JwPagination);

export default {
  name: 'home',
  components: {
    ImageCard,
    OverlayCard,
    Spinner,
    JwPagination
  },
  data () {
    return {
      tag: '',
      loading: false,
      images: [],
      slectedFile: '',
      totalImages: 0,
      perPage: 12,
      currentPage: 11,
      pageOfItems: []
    }
  },
  computed: {
    cleanImages () {
      return this.images.filter(image => image.url_n)
    },
    isTagEmpty () {
      return !this.tag || this.tag.length === 0
    }
  },
  methods: {
    search () {
      if (!this.isTagEmpty) {
        this.loading = true
        this.fetchImages(this.currentPage).then(response => {
          // this.loading = true
          console.log(response.data)
          this.images = response.data.photos.photo
          this.totalPhotos = response.data.photos.total
          this.currentPage = response.data.photos.page
          console.log('Searching for: ', this.tag)
          console.log('всего фото: ' + this.totalPhotos + ' текущая страница: ' + this.currentPage)
          this.loading = false
          this.tag = ''
        })
      }
    },
    onChangePage (pageOfItems) {
      // update page of items
      this.pageOfItems = pageOfItems
    },
    showGallery (evt) {
      console.log(evt)
      // let image = evt.path[0]
      // this.slectedFile = evt.target
      // console.log(this.slectedFile)
    },
    fetchImages (page) {
      return axios({
        method: 'get',
        url: 'https://api.flickr.com/services/rest/',
        params: {
          method: 'flickr.photos.search',
          api_key: config.api_key,
          tags: this.tag,
          extras: 'url_n, owner_name, date_taken, views',
          page: page,
          format: 'json',
          nojsoncallback: 1,
          per_page: this.perPage,
          tag_mode: 'any'
        }
      })
    }
  }
}
</script>
<style lang='scss'>
.screen-reader-only {
  height: 1px;
  width: 1px;
  position: absolute;
  left: -100000px;
}
.text-centered {
  text-align: center;
}
.container {
  margin: 0 auto;
  max-width: 1100px;
  @media only screen and (max-width: 799px) {
    max-width: 100%;
    margin: 0 1.5rem;
  }
}
.card-grid {
  list-style: none;
  margin: .5rem 0;
  padding: 0;
  display: flex;
  // align-items: flex-start;
  flex-wrap: wrap;
}
.navbar {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1rem;
  background: #F0F0F0;
}
.searchbar {
  width: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
  @media only screen and (max-width: 549px) {
    width: 100%;
    label {
      width: 80%;
    }
  }
}
.searchbar-input {
  padding: .5rem 1rem;
  border-radius: 6px;
  font-size: 1rem;
  border: 1px solid gray;
  min-width: 300px;
  @media only screen and (max-width: 549px) {
    min-width: 0;
    width: 100%;
  }
}
.btn {
  padding: .5rem 1rem;
  font-size: 1rem;
  border-radius: 6px;
  background: transparent;
  border: none;
  cursor: pointer;
}

.btn--green {
  background: #42b983;
  color: white;
  font-weight: bold;
}
.btn--go {
  padding: .5rem 2rem;
  margin-left: 1rem;
}
</style>
