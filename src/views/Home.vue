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
      <ul v-else class='card-grid' @click.prevent='showGallery'>
        <image-card v-for='image in cleanImages' :key='image.id' :image='image' />
      </ul>
    </div>
    <!-- <overlay-card/> -->
      <div class='clearfix btn-group col-md-2 offset-md-5'>
        <button type='button' class='btn btn-sm btn-outline-secondary' v-if='page != 1' @click='page--'> before </button>
        <button type='button' class='btn btn-sm btn-outline-secondary' v-for='pageNumber in pages.slice(page-1, page+5)' @click='page = pageNumber' :key='pageNumber.id'> {{pageNumber}} </button>
        <button type='button' @click='page++' v-if='page < pages.length' class='btn btn-sm btn-outline-secondary'> next </button>
      </div>
  </div>
</template>

<script>
import config from '../../config'
import axios from 'axios'
import Spinner from 'vue-simple-spinner'
import ImageCard from '@/components/ImageCard'
// import OverlayCard from '@/components/OverlayCard'

export default {
  name: 'home',
  components: {
    ImageCard,
    // OverlayCard,
    Spinner
  },
  data () {
    return {
      tag: '',
      loading: false,
      images: [],
      slectedFile: '',
      totalImages: 0,
      perPage: 12,
      // page: 2,
      pages: [],
      totalPages: '',
      page: 1
    }
  },
  computed: {
    cleanImages () {
      return this.images.filter(image => image.url_n)
    },
    isTagEmpty () {
      return !this.tag || this.tag.length === 0
    },
    displayedImages () {
      return this.paginate(this.images)
    }
  },
  methods: {
    search (page) {
      if (!this.isTagEmpty) {
        this.loading = true
        this.fetchImages(this.page).then(response => {
          // this.loading = true
          console.log(response.data)
          this.images = response.data.photos.photo
          this.totalPhotos = response.data.photos.total
          this.page = response.data.photos.page
          this.totalPages = response.data.photos.pages
          console.log('Searching for: ', this.tag)
          console.log('всего фото: ' + this.totalPhotos + ' текущая страница: ' + this.page + ' Всего страниц: ' + this.pages)
          this.loading = false
          // this.tag = ''
          for (let i = 1; i <= this.totalPages; i++) {
            this.pages.push(i)
          }
          console.log(this.pages)
        })
      }
    },
    setPages () {
      // this.totalPages = this.pages
      for (let i = 1; i <= this.totalPages; i++) {
        this.pages.push(i)
      }
      console.log(this.pages)
    },
    paginate (images) {
      let page = this.page
      let perPage = this.perPage
      let from = (page * perPage) - perPage
      let to = (page * perPage)
      return images.slice(from, to)
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
