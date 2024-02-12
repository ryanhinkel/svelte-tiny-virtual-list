<script>
	import VirtualList from '$lib/VirtualList.svelte';
	import InfiniteLoading from 'svelte-infinite-loading';

	const api = 'https://hn.algolia.com/api/v1/search_by_date?tags=story';

	let page = 1;
	let listHeight = 0;
	let list = [];

	function infiniteHandler({ detail: { loaded, complete } }) {
		fetch(`${api}&page=${page}`)
			.then((response) => response.json())
			.then((data) => {
				if (data.hits.length) {
					page += 1;
					list = [...list, ...data.hits];
					loaded();
				} else {
					complete();
				}
			});
	}
</script>

<div class="app">
	<header class="hacker-news-header">
		<a target="_blank" href="http://www.ycombinator.com/">
			<img src="https://news.ycombinator.com/y18.gif" alt="Logo" />
		</a>
		<span>Hacker News</span>
	</header>

	<div class="list" bind:offsetHeight={listHeight}>
		<VirtualList height={listHeight} itemSize={42} itemCount={list.length}>
			<div slot="item" let:index let:style {style} class="hacker-news-item" data-num={index + 1}>
				<a target="_blank" href={list[index].url}>{list[index].title}</a>
				<p>
					<span>{list[index].points}</span>
					points by
					<a target="_blank" href="https://news.ycombinator.com/user?id={list[index].author}"
						>{list[index].author}</a
					>
					|
					<a target="_blank" href="https://news.ycombinator.com/item?id={list[index].objectID}"
						>{list[index].num_comments} comments</a
					>
				</p>
			</div>

			<div slot="footer">
				<InfiniteLoading on:infinite={infiniteHandler} />
			</div>
		</VirtualList>
	</div>
</div>

<style>
	:global(body) {
		padding: 28px 0 0 0;
		background-color: #f6f6ef;
	}
	.app {
		height: 100%;
		width: 100%;
		display: flex;
	}
	.list {
		flex-grow: 1;
	}
	.list :global(.virtual-list-wrapper) {
		overflow: visible;
		overflow-x: hidden;
		white-space: nowrap;
	}
	.hacker-news-header {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		padding: 4px 20px;
		line-height: 14px;
		background-color: #f60;
	}
	.hacker-news-header img {
		border: 1px solid #fff;
		vertical-align: middle;
	}
	.hacker-news-header span {
		font-family: Verdana, Geneva, sans-serif;
		font-size: 14px;
		font-weight: bold;
		vertical-align: middle;
	}
	.hacker-news-item {
		padding: 10px 10px 10px 40px;
		line-height: 16px;
		font-size: 14px;
	}
	.hacker-news-item::before {
		content: attr(data-num) '.';
		float: left;
		margin-left: -40px;
		width: 32px;
		color: #888;
		text-align: right;
	}
	.hacker-news-item > a {
		color: #333;
	}
	.hacker-news-item > a:hover {
		color: #000;
	}
	.hacker-news-item p {
		margin: 0;
		font-size: 12px;
	}
	.hacker-news-item p,
	.hacker-news-item p a {
		color: #888;
	}
	.hacker-news-item p a:not(:hover) {
		text-decoration: none;
	}
</style>
