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
		<p>我是一位前端开发者.我喜爱的语言是javascript.我喜欢开源,所以我把我的所有都放在了github上,我还参与了其他人的开源项目，并做出了一点点贡献，我想这是不错的体验.</p>
		<ul class="actions">
			<li><a href="landing.html" class="button next">Learn More</a></li>
		</ul>
	</div>
</section>

</div>

