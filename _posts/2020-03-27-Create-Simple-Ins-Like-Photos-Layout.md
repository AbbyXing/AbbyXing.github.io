---
layout: post
title:  "Create A Simple Ins-Like Style Photos Layout (CSS)"
description: use flex display to create photos layout.
---
# Start
It's always good to have a simple but pretty layout for photos. this layout is from Instagram web, easy, simple but chic, which I'm really obssessed with:  
![ins-screen-shot][ins-screen-shot]
If you also like this style, let's just start creating a ins-like phtots layout, using just html and CSS, will be very easy. The final look shall be look like this:  
![final-look][final-look]


# "flexbox"
We'll be using the ***flexbox*** to layout all photos. ***flexbox*** was a one-dimensional layout mode, which deals with layout in one dimension at a time — either as a row or as a column. To create a flex container, we set the value of the "container" of our photo items ***display*** property to ***flex***. First let's take a look at an example of what ***flexbox*** can do.
```html
<style>
    .box {
        display: flex;
    }
    .item {
        width: 100px;
        height: 100px;
        border: 2px solid blue;
    }
</style>

<div class="box">
    <div class="item">One</div>
    <div class="item">Two</div>
    <div class="item">Three</div>
</div>
```
The result looks like this:  
![flex-nowrap][flex-nowrap]  

If your items are large and can't be alined in one rwo, we use ***flex-wrap: wrap;*** to allow our flex items to wrap onto multiple lines. For example:
```html
<style>
    .box {
        display: flex;
        flex-wrap: wrap;
    }
    .item {
        width: 200px;
        height: 200px;
        border: 2px solid blue;
    }
</style>

<div class="box">
    <div class="item">One</div>
    <div class="item">Two</div>
    <div class="item">Three</div>
</div>
```
The result looks like this:  
![flwx-wrap][flwx-wrap]


# Code
The final ***html*** code looks like this:
```html
<div class = "container">
	<ul class="album">
		<li class="album-item"><img src="your-photo-url"></li>
		<li class="album-item"><img src="your-photo-url"></li>
		<li class="album-item"><img src="your-photo-url"></li>
		<li class="album-item"><img src="your-photo-url"></li>
		<li class="album-item"><img src="your-photo-url"></li>
		<li class="album-item"><img src="your-photo-url"></li>
	</ul>
</div>
```
And the ***CSS*** code looks like this:
```html
li {
	list-style-type:none
}
.container {
	margin: 10em 10em; /*you can change the margin*/
}
.album {
	display: flex;
	flex-direction: row;
	flex-wrap: wrap;
	align-items: stretch;
}
.album-item img {
	height: 250px; /*you can change your img size*/
	margin: 1em 1em; /*you can change the margin*/
}
```  
### Enjoy!



[ins-screen-shot]: https://res.cloudinary.com/daxtpgi3q/image/upload/v1585293652/blogs/2020-03-27-ins-like-photo-layout/Screen_Shot_2020-03-27_at_3.20.29_AM_csdaoq.png
[final-look]: https://res.cloudinary.com/daxtpgi3q/image/upload/v1585293549/blogs/2020-03-27-ins-like-photo-layout/Screen_Shot_2020-03-27_at_2.13.22_AM_tizgou.png
[flex-nowrap]: https://res.cloudinary.com/daxtpgi3q/image/upload/v1585293555/blogs/2020-03-27-ins-like-photo-layout/Screen_Shot_2020-03-27_at_3.18.44_AM_glrklb.png
[flwx-wrap]: https://res.cloudinary.com/daxtpgi3q/image/upload/v1585293553/blogs/2020-03-27-ins-like-photo-layout/Screen_Shot_2020-03-27_at_3.18.17_AM_xm72yf.png