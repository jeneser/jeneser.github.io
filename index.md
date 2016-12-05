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
		<p>我是一位前端开发者.喜爱javascript.深深地热爱着开源,我把很多东西放在了github上,还参与了一些其他人的开源项目，并做出了一点点贡献，我认为这将是不错的体验。
		我喜欢去图书馆，读一些书，算是一个文艺点的青年程序员吧！
		《思考-快与慢》启蒙了我的思想，我不断地思考周遭的人与事，一窥世间百态，汲其精华价值，不亦乐乎。
		我还有一个六字座右铭：开放，思考，勇气
		我鼓励自己每天进步一点点...</p>
		<ul class="actions">
			<li><a href="landing.html" class="button next">Learn More</a></li>
		</ul>
	</div>
</section>

</div>

