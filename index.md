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
		<p>我是一个会写一些node的前端探路者。</p>

		<p>喜爱javascript.深深地热爱着开源与github．参与了一些其他人的开源项目，并做出了一点点贡献，我认为这将是不错的体验。</p>
		<p>我喜欢去图书馆，读一些书，不论是技术书，还是文学书，都有涉略．算是一个文艺点的青年程序员吧！
		我喜欢读《思考-快与慢》《黑客与画家》</p>
		<ul class="actions">
			<li><a href="landing.html" class="button next">Learn More</a></li>
		</ul>
	</div>
</section>

</div>
