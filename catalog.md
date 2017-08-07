---
layout: landing
title: POSTS
description: It mainly shares front-end technology, but not only the front-end.<br />All I know is what I shared.
image: assets/images/pic07.jpg
nav-menu: yes
---

<!-- Banner -->
<!-- Note: The "styleN" class below should match that of the header element. -->
<section id="banner" class="style2">
	<div class="inner">
		<span class="image">
			<img src="{{ site.baseurl }}/{{ page.image }}" alt="" />
		</span>
		<header class="major">
			<h1>POSTS</h1>
		</header>
		<div class="content">
			<p>{{ page.description }}</p>
		</div>
	</div>
</section>

<!-- Main -->
<div id="main">

<!-- One -->
<section id="one">
	{% for post in site.posts limit:20 %}
	{% if site.tiles-source == 'posts' %}
		<div class="inner">
			<header class="major">
				<h2>
					<a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
				</h2>
			</header>
			<p>{{ post.description }}......</p>
		</div>
	{% endif %}
	{% endfor %}
</section>

<!-- Three -->
<section id="three">
	<div class="inner">
		<header class="major">
			<h2>Massa libero</h2>
		</header>
		<p>Nullam et orci eu lorem consequat tincidunt vivamus et sagittis libero. Mauris aliquet magna magna sed nunc rhoncus pharetra. Pellentesque condimentum sem. In efficitur ligula tate urna. Maecenas laoreet massa vel lacinia pellentesque lorem ipsum dolor. Nullam et orci eu lorem consequat tincidunt. Vivamus et sagittis libero. Mauris aliquet magna magna sed nunc rhoncus amet pharetra et feugiat tempus.</p>
		<ul class="actions">
			<li><a href="generic.html" class="button next">Get Started</a></li>
		</ul>
	</div>
</section>

</div>
