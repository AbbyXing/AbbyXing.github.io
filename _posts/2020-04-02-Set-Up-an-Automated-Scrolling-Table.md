---
layout: post
title:  "Set Up an Automated Scrolling Notice Board (HTML, Javascript, CSS)"
description: understand "scrollTop" and "scrollHeight" and make your table scrolling.
---

An automated scrolling table is effective and efficient to show news or notices on the website. It also makes your website look not that static. In this article we'll be using HTML &lt;table&gt;, CSS and Javascript to create a automated scrolling up notice board. Let's first take a look at final result:

# Final Result
![final-look][final-look]

# scrollTop And scrollHeight
[Element.scrollTop][scrollTop] and [Element.scrollHeight][scrollHeight] are two properties we utilize to make that scrolling happend. According to the definitions, `Element.scrollTop` is to return or set the number of pixels an element is scrolled vertically. If the element’s content doesn’t generate a scroll bar, then its scrollTop value is 0. `Element.scrollHeight` is a measurement of the height of an element's content, including content not visible on the screen due to overflow.  
  
  In short, `scrollHeight` to some extents, gives you how high this element's content really is, and `scrollTop`, tells you how high the element's content has already been scrolled up. After understanding those two properties, we can start to play around with them and see what scrolling effect we can achieve.


# Code Source With Comments

HTML Code:
```html
<div class="container">
  <h2>Notice Board</h2>
  <div id="scrolling-wrapper">
    <table id="scrolling-table">
    </table>
  </div>
</div>
```  

CSS Code:
```css
body {
	background-color: black;
}
h2 {
  	padding: 0;
  	font-size: 2em;
  	margin-bottom: 3px;
  	color: #8400ff;
}

.container {
  	margin: 100px 450px;
}
table, td {
  	border: none;
  	font-size: 1.1em;
}

table {
  	width: 100%;
  	color: #ff00f2;
}

td {
  	padding: 1px 1px;
}

#scrolling-wrapper {
	width: 100%;
  	height: 150px;
  	overflow: hidden; /*hide overflow table content*/
  	background-color: #85fff3;
}
```  

Javascript Code:
```javascript
var ppl = ["Alivia Nolan", "Zunairah Werner", "Lacey Flynn", "Jaye Hurst", "Jimmie Thatcher", "Rueben Randall", "Clarice Derrick", "Leila Sweeney", "Luther Matthams", "Brendan Rose"];

// select elements
var scrollTable = document.querySelector("#scrolling-table");
var scrollWrapper = document.querySelector("#scrolling-wrapper");

// insert data into table
for(var i = 0; i < 10; i++) {
	var row = scrollTable.insertRow(0);
	var cell1 = row.insertCell(0);
	var cell2 = row.insertCell(1);
	var cell3 = row.insertCell(2)
	var cell4 = row.insertCell(3);
	cell1.textContent = ppl[i];
	cell2.textContent = "donates";
	cell3.textContent = (i + 1).toString();
	cell4.textContent = "face masks.";
}

// repeat the table content to ensure the infinite scrolling effect
scrollTable.innerHTML += scrollTable.innerHTML;

// scrolling function
function scrolling() {
	/* when the table has been scrolled half way or more than half
	way up, we manually "scroll it back" half of the table height,
	so that the contents that are displaying in the wrapper will
	stay "same" since we've already duplicated all contents.
	*/
	if (scrollWrapper.scrollTop >= 0.5 * scrollTable.scrollHeight) {
		scrollWrapper.scrollTop -= 0.5 * scrollTable.scrollHeight;
	} else {
		// otherwise, we just scroll the table up little bit
		scrollWrapper.scrollTop++;
	}
}

// set timer for scrolling (larger number will make scrolling slower)
let timer = setInterval(scrolling, 30);
// when mouseover, stop scrolling
scrollWrapper.addEventListener('mouseover', () => clearInterval(timer), false);
// when mouseout, continue scrolling
scrollWrapper.addEventListener('mouseout', () => {timer = setInterval(scrolling, 20)}, false);
````  

## Thanks for reading and happy coding td!


[final-look]: https://res.cloudinary.com/daxtpgi3q/image/upload/v1585826172/blogs/2020-04-02-scrolling-table/ezgif.com-crop_pplitk.gif
[scrollTop]: https://www.geeksforgeeks.org/html-dom-scrolltop-property/
[scrollHeight]: https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollHeight