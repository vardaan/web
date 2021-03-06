<template lang="pug">
  ais-index(:search-store="searchStore" index-name="videos")
    header
      .container
        nav.level(style="min-height: 80px")
          .level-left
            .level-item
              a(href="/"): img.logo(src="/logo.png" srcset="/logo.svg")
          .level-item.has-text-centered(v-if="!newMode")
              Input(placeholder="Search for videos...")
          .level-item.has-text-centered(v-else)
              Input(v-if="!speaker && !tag && !channel" placeholder="Search for videos...")
          .level-right.has-text-lato
            .level-item.links.is-size-10
              a.has-text-white(href="https://devternity.com" target="_blank")
                span inspired by  
                strong DevTernity
              | &nbsp;&nbsp;
              NightMode
    section.section
          .container
            .columns
              .column.is-one-quarter(v-if="newMode")
                p.buttons
                  router-link.button.is-small.is-outlined.is-hidden-tablet(v-if="speaker || tag || channel" :to="{ name: 'search' }")
                    span {{speaker || tag || channel}}
                    span.icon.is-small: i.fas.fa-times
                  a.button.is-small.is-hidden-tablet(@click="$refs.tagPicker.expand()"): span.icon.is-small: i.fas.fa-hashtag
                  a.button.is-small.is-hidden-tablet(@click="$refs.speakerPicker.expand()"): span.icon.is-small: i.far.fa-user-circle
                  a.button.is-small.is-hidden-tablet(@click="$refs.channelPicker.expand()"): span.icon.is-small: i.fab.fa-youtube

                ExpandableTags(ref="tagPicker" icon="fas fa-hashtag" title="Tags" :items="tags" :limit="10" :route="routeToTag")
                    template(slot-scope="slot") {{slot.item.tag}}

                ExpandableTags(ref="speakerPicker" icon="far fa-user-circle" title="Speakers" :items="speakers" :limit="10" :route="routeToSpeaker")
                    template(slot-scope="slot")
                      span {{slot.item.name}}

                ExpandableTags(ref="channelPicker" icon="fab fa-youtube" title="Channels" :items="channels" :limit="10" :route="routeToChannel")
                    template(slot-scope="slot") {{slot.item.title}}

              .column.is-one-quarter.is-hidden-touch(v-else)
                .columns(v-if="newVideos.length && !newOnly")
                  .column
                    .content
                      p 
                        b {{newVideos.length}} 
                        | new videos since yesterday!
                      a.button.is-info.is-outlined(v-on:click="showNewVideos()") Show me
                .columns
                  .column
                    h1.title Tags
                    ais-refinement-list.is-capitalized(:class-names="{'ais-refinement-list__count': 'tag'}" attribute-name="tags" :sort-by="['count:desc', 'name:asc']")
                .columns(v-if="!speaker")
                  .column
                    h1.title Speaker
                    ais-refinement-list.is-capitalized(:class-names="{'ais-refinement-list__count': 'tag'}" attribute-name="speaker.name" :sort-by="['count:desc', 'name:asc']")
                .columns
                  .column
                    h1.title Channel
                    ais-refinement-list.is-is-capitalized(:class-names="{'ais-refinement-list__count': 'tag'}" attribute-name="channelTitle" :sort-by="['count:desc', 'name:asc']")     
                .columns
                  .column
                    h1.title Year
                    YearRange
              .column
                .columns
                  .column
                    .columns
                      .column(v-if="!newMode")
                        ActiveFilters(:speaker="speaker")
                      .column.is-hidden-mobile(v-else)
                        router-link.button.is-small.is-outlined(v-if="speaker || tag || channel" :to="{ name: 'search' }")
                          span.is-capitalized(v-if="tag || channel") {{tag || channel}}
                          span.is-lowercased(v-if="speaker") @{{speaker}}
                          span.icon.is-small: i.fas.fa-times
                      .column
                        .field.is-grouped-multiline.is-grouped.is-grouped-right(v-if="newMode")
                          .control
                            Sorting
                    .loading(v-if="loading")
                      .notification
                        p
                          | Searching for the best tech videos 👨🏻‍💻...
                          br
                          br
                          | We're working on reducing Cloud Function cold start time.
                    .loaded(v-else)
                      ais-no-results
                        template(slot-scope="props")
                          .notification
                            p
                              | No videos matching your query. Please 
                              a(href="https://github.com/watch-devtube/contrib" target="_blank") contribute on GitHub
                              | .
                      ais-results#videos.columns.is-multiline
                        template(slot-scope="{ result }")
                          .column.is-6.is-flex-tablet.is-4-widescreen
                            VideoCard(:newMode="newMode" :tags="result.tags" :featured="result.featured" :tagsClickable="true" :speaker="result.speaker" :creationDate="result.creationDate" :recordingDate="result.recordingDate" :duration="result.duration" :views="result.views" :satisfaction="result.satisfaction" :title="result.title" :id="result.objectID" :channel="result.channelTitle")
    section.section
      .container
        .columns
          .column.has-text-right
            a(v-if="!newMode" href="https://www.algolia.com" target="_blank"): img(src="/search-by-algolia.png" srcset="/search-by-algolia.svg")
            br
            br
            nav.paging(role="navigation" aria-label="pagination")
             ais-pagination.pagination(:class-names="{'ais-pagination': 'pagination-list', 'ais-pagination__item': 'page', 'ais-pagination__link': 'pagination-link', 'ais-pagination__item--previous': 'is-hidden', 'ais-pagination__item--next': 'is-hidden', 'ais-pagination__item--active': 'is-current'}")
</template>
<style lang="scss">

.has-text-lato {
  font-family: Lato
}

header {
  background-color: #343d46;
  padding: 30px;

  @media only screen and (max-width: 768px) {
    .logo {
      width: 70px;
      margin-bottom: 10px;
    }
  }

  a {
    color: white;
  }

  input {
    -webkit-appearance: none;
    outline: none;
    color: white;
    font-size: 15px;
    font-weight: 100;
    background-color: #343d46;
    padding: 16px 26px 16px 52px;
    border: 1px solid #6498cf;
    border-radius: 3px;
  }
}

  input::placeholder{
    color: #fff;
  }

.paging  {
  .pagination-list {
    justify-content: center;
  }

  .is-current a {
    background-color: #343d46;
    color: white;
  } 
}

</style>
<script>
import { createFromAlgoliaCredentials } from 'vue-instantsearch'
import { createFromAlgoliaClient } from 'vue-instantsearch'

import VideoCard from './VideoCard.vue'
import Sorting from './Sorting.vue'
import ActiveFilters from './ActiveFilters.vue'
import YearRange from './YearRange.vue'
import ExpandableTags from './ExpandableTags.vue'
import NightMode from './NightMode.vue'
import Input from './Input.vue'

export default { 
  props: {
    showNew: { type: Boolean, required: false, default: false },
    speaker: { type: String, required: false },
    channel: { type: String, required: false },
    tag: { type: String, required: false }
  },
  data: function() {
    return {
      loading: false,
      tagsCollapsed: true,
      speakersCollapsed: true,
      channelsCollapsed: true,
      newMode: window.fastrMode,
      newVideos: window.newVideos
    };
  },
  watch: {
    '$route': 'fetch'
  },  
  created() {
    let that = this

    let fastr = {
      search(requests) {
        that.$Progress.start()
        that.loading = true
        return fetch('/search', {
          method: 'post',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({ requests }),
        }).then(res => {
          let json = res.json()
          that.$Progress.finish()
          that.loading = false
          return json
        })
      },
      addAlgoliaAgent(agent) {}
    };

    let searchStore = window.fastrMode ?
      createFromAlgoliaClient(fastr) :
      createFromAlgoliaCredentials(
        'DR90AOGGE9',
        'c2655fa0f331ebf28c89f16ec8268565'
    );

    if (window.speaker && !window.fastrMode) {
      searchStore.queryParameters = { disjunctiveFacets: ['speaker.twitter'] };
      searchStore.algoliaHelper.addDisjunctiveFacetRefinement('speaker.twitter', window.speaker)
    }

    searchStore.queryParameters = {
      hitsPerPage : 21
    }

    this.searchStore = searchStore

    this.fetch()
  },
  computed: {
    newOnly() {
      return this.searchStore.algoliaHelper.state.filters && 
        this.searchStore.algoliaHelper.state.filters.includes('objectID')
    },
    tags() {
      return window.featured.tags
    },
    speakers() {
      return window.featured.speakers
    },
    channels() {
      return window.featured.channels
    }
  },
  methods: {
    routeToTag(item) {
      return { name: 'tag', params: { tag: item.tag } }
    },
    routeToSpeaker(item) {
      return { name: 'speaker', params: { speaker: item.twitter } }
    },
    routeToChannel(item) {
      return { name: 'channel', params: { channel: item.title } }
    },
    fetch() {
      this.newVideos = window.newVideos
      this.newMode = window.fastrMode


      this.searchStore.stop()

      if (this.newMode) {
        // ## start 
        // when seach query is present, tag clicking must reset it
        this.searchStore.query = undefined
        // ## end

        this.searchStore.queryParameters = { refinement : undefined }

        this.searchStore.queryParameters = { sortOrder: this.$cookie.get('sortBy') || '-featured' }


        if (this.speaker) {
          this.searchStore.queryParameters = { refinement: { 'speaker.twitter' : this.speaker } }
        }

        if (this.tag) {
          this.searchStore.queryParameters = { refinement: { 'tags' : { $contains: this.tag } } }
        }
        if (this.channel) {
          this.searchStore.queryParameters = { refinement: { 'channelTitle' : this.channel } }
        }
      }

      this.searchStore.start()
      this.searchStore.refresh()

    },
    showNewVideos: function() {
      let newOnly = this.newVideos.map(id => `objectID:${id}`).join(' OR ')
      this.searchStore.algoliaHelper.setQueryParameter('filters', `(${newOnly})`)
    }
  },
  components: { 
    ExpandableTags,
    NightMode,
    ActiveFilters, 
    VideoCard, 
    YearRange, 
    Sorting,
    Input
  }
}
</script>
