
<script>
export default {
    name: 'BlogPostList',
    props: {
        list: {
            type: Array,
            default: () => []
        }
    },
    data() {
        return {
            displayRange: {
                end: 4
            },
            selectedTag: ''
        }
    },
    computed: {
        filteredList() {
            const props = this.$options.propsData
            if (props) {
                if (props.list && props.list.length > 0) {
                    return props.list.filter(item => {
                        const isBlogPost = item.path.includes("/blog/")
                        const isReadyToPublish = new Date(item.frontmatter.date) <= new Date()
                        const hasTags = item.frontmatter.tags && item.frontmatter.tags.includes(this.selectedTag)
                        const shouldPublish = this.selectedTag ? isBlogPost && isReadyToPublish && hasTags : isBlogPost && isReadyToPublish
                        
                        if (shouldPublish) {
                            return item
                        }
                    }).sort((a, b) => new Date(b.frontmatter.date) - new Date(a.frontmatter.date))
                }
            }
        },
    },
    methods: {
        loadMore() {
            this.displayRange.end += 5
        },
        updateSelectedTag(tag) {
            this.selectedTag = tag
        }
    }
}
</script>

<template>
	<div class="blog-list__container"> 
        <div class="blog-list__header">
            <h1>Blog Posts</h1> 
            <div style="margin-left:20px;" class="tooltip-ex"><strong><i class="fas fa-info-circle"></i></strong>
                <span class="tooltip-ex-text tooltip-ex-bottom">Here are the blog posts</span>
            </div>
        </div>

        <h2>Most Recent</h2>

        <div 
            v-if="selectedTag"
            class="filtered-heading"
        >
            <h2>
                Filtered by {{ selectedTag }} tag
            </h2>
            <button
                type="button"
                @click="selectedTag = ''"
                class="btn clear-filter-btn"
            >
                Clear filter
            </button>
        </div>
        <ul class="blog-list">
            <li v-for="(item, index) in filteredList"
                class="blog-list__item">
                <BlogPostPreview 
                    v-show="index <= displayRange.end"
                    :excerpt="item.frontmatter.excerpt" 
                    :path="item.path"
                    :publishDate="item.frontmatter.date"
                    :tags="item.frontmatter.tags"
                    :title="item.frontmatter.title"
                    @updateSelectedTag="updateSelectedTag"
                />
            </li>
        </ul>

        <div v-if="displayRange.end <= filteredList.length" class="pagination">
            <button
                @click="loadMore"
                class="button--load-more"
                type="button"
            >
                Load More
            </button>
        </div>
    </div>
</template>

<style lang="scss" scoped>
</style>