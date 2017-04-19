---
layout: home
title: 主页
landing-title: Hi, my name is Jeneser
description:
image:
author:
nav-menu:
---

<!-- Banner -->
<section id="banner" class="major">
	<div class="inner">
		<header class="major">
			<h1>{{ page.landing-title }}</h1>
		</header>
		<div class="content">
			<p style="text-transform: uppercase;">{{ site.description }}</p>
			<ul class="actions">
				<li><a href="#one" class="button next scrolly">Get Started</a></li>
			</ul>
		</div>
	</div>
</section>

<!-- Main -->
<div id="main">

<!-- One -->
{% include tiles.html %}

<!-- Two -->
<section id="two">
	<div class="inner">
		<header class="major">
			<h2>About Me</h2>
		</header>
		<p>前端，轮子爱好者，社区灌水党</p>

		<p>喜爱javaScript，深深地热爱着开源与github。参与了一些团队的开源项目，并做出了一点点贡献，我认为这将是不错的体验。</p>
		<p>喜欢读书，去图书馆，技术书，文学书，都读。大概是一个文艺点的青年程序员吧！</p>
		<ul class="actions">
			<li><a href="profile.html" class="button next">Learn More</a></li>
		</ul>
	</div>
</section>

</div>
