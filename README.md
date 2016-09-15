Index.html

Initally the DOM Content Loading time is around 60-70msec and it takes an addional 60-70msec to the onload event to fire.
This means that the page initial loading time is most likely above 1sec.

Initial Path: 2 render blocking CSS and 2 parser bloking JS (inline and external analytics scripts).
Number of critcal resources: 5.
Number of minimum critical path length (roundtrips): 2.
Total critical KB: 24,5 KB 

After the browser sends its request and gets its response, it starts parse the HTML.
Requests are sent out for all the stylesheets and scripts.
	First possible optimisation to look at the parsing and renderblocking scripts and stylesheets.
	
	Webfonts are needed for the optimal rendering of the page and are part of the Critical Rendering Path.
		However without the webfonts, the page load time drops drastically as the DCL drops between 30-50msec and the final onload is between 50-70msec (all-in-all)
		Additionally the total critical KB drops by 900B.
	Style.css is also needed for rendering the page and is part of the CRP.
	It seems that print.css should not be blocking as it is usally not used and not part of the CRP.

	Analytics.js is most certainly should not be part of the the CRP and should run asynchronously.
	Prefmatters.js is monitoring the CRP and already asynchronous with the critical path.

After the DOM is completed CSSOM should be built, then all the JS should run and finally layout and render should be carried out before painting.
	It seems to be an issue with the images (pizza.jpg in particular) as it takes an awful lot of time for the browser to gather the data about the images.
		Images should be optimised for rendering.

DOM Content Loading time after optimisation: around 15msec, onload event fires around 28msec.
New Critical Rendering Path:
Number of Resources: 4 (The HTML, an inline JS of analytics and an inline CSS styling for the above the fold content and the webfont's CSS).
Number of minimum CP length: 2.
Total critical KB: 3,5 KB

In addition to the sources that were touched upon in the course:
http://stackoverflow.com/questions/19374843/css-delivery-optimization-how-to-defer-css-loading
-----------------------------------------------------------------------------------------------------------------

Pizza.html

Initially the time to Generate is around 35msec.
Initially the avarage time to Generate 10 frames is around 50msec.

In the Timeline we can see a JS script firing on load and also on scroll event causing Forced Synchronous Layout error.
	It seems that the function that causing the problem is "updatePositions" starting in line 501 and the "document.addEventListener" in line 527 that calls updatePositions.
		In updatePositions, it seems that scrollTop and left could trigger the FSL error.
		Heavy calcuations are not necessary to be made on every turn of the loop. Therefore, body.scrollTop calculation should be moved outside of the loop contained in updatePositions. This seemingly solves the problem of Forced Synchronous Layout when scrolling.
		On loading the page however, document.addEventListener still triggers a FSL error by calling updatePositions.
The generator for moving pizzas in the background generates an insane amout of pizzas at once.
	The amount of pizzas can be reduced to 21 without any visible change.
It also seems that the moving pizzas in the background are not on separate composite layers, thus forcing the whole page to be repainted in each frame.

The initial time to resize pizzas is around 175msec.

"determineDx" function in line 425 utilizing .offsetWhidth, triggering a Forced Synxhronous Layout error.
	Using methods pulled from the course we can simplify the code by large cutting out all the hard calculations.

The new time to Generate is around 20msec.
The new time to Generate 10 frames is around 0.3msec.
The new time to resize pizzas is around 0.5msec.

In addition to the sources that were touched upon in the course:
https://developer.chrome.com/devtools/docs/demos/too-much-layout
http://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/