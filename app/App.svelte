<page class="page">
    <actionBar title="Svelte HN" class="action-bar"></actionBar>
    <gridLayout rows="auto, *">
        <listView row="0" items="{items}" on:loadMoreItems={loadNextPage} height="100%" on:itemTap={on_show_article}>
            <Template let:item>
                <stackLayout>
                    <Summary {item} on:showcomments={on_show_comments}></Summary>
                </stackLayout>
            </Template>
        </listView>
        <activityIndicator row="0" busy="{loading}" verticalAlignment="bottom" horizontalAlignment="right" visibility="{ loading ? 'visible' : 'collapse' }"></activityIndicator>
    </gridLayout>
</page>
<style>
    .action-bar {
        background-color: #ff6600;
        color: #FFFFFF;
    }
    listView {
        background-color: #f6f6ef;
        color: #828282;
    }
    activityIndicator {
        width: 24;
        height: 24;
        color: black;
        opacity: 0.2;
        margin: 10;
    }

</style>

<script>
    import { onMount } from 'svelte'
    import { showModal } from 'svelte-native'
    import { Template } from 'svelte-native/components'
    import Summary from './Summary.svelte'
    import Article from './Article.svelte'

    let items = [];
    let page = 1;
    let loading = true;

    async function loadPage(page) {
        loading = true;
        return await fetch(`https://node-hnapi.herokuapp.com/news?page=${page}`)
            .then(r => r.json())
            .finally(loading = false);
    }

    async function refresh() {
        page = 1;
        items = await loadPage(1);
    }

    async function loadNextPage() {
        page = page + 1;
        items = items.concat(await loadPage(page));
    }

    function show_article(item, show_comments) {
        showModal({ page: Article,  props: { item_summary: item, showComments: show_comments} , fullscreen: true});
    }

    function on_show_comments(e) {
        show_article(e.detail.item, true)
    }

    function on_show_article(e) {
        show_article(items[e.index], false)
    }

    onMount(refresh);

</script>