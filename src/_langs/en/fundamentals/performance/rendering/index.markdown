---
layout: section
title: "Rendering Performance"
description: "Users notice if sites and apps don't run well, so optimizing rendering performance is crucia!"
introduction: "Users of today’s web <a href=\"http://paul.kinlan.me/what-news-readers-want/\">expect that the pages they visit will be interactive and smooth</a> and that’s where you need to increasingly focus your time and effort. Pages should not only load quickly, but also run well; scrolling should be stick-to-finger fast, and animations and interactions should be silky smooth."
article:
  written_on: 2015-03-20
  updated_on: 2015-03-20
  order: 3
id: rendering-performance
collection: performance
priority: 0
authors:
  - paullewis
notes:
  csstriggers:
    If you want to know which of the three versions above changing any given CSS property will trigger head to <a href="http://csstriggers.com">CSS Triggers</a>. And if you want the fast track to high performance animations, read the section on changing compositor-only properties.
  pipelinecosts:
    Performance is the art of avoiding work, and making any work you do as efficient as possible. In many cases it's about working with the browser, not against it. It’s worth bearing in mind that the work listed above in the pipeline differ in terms of computational cost; some tasks are more expensive than others!
---
{% wrap content%}

<img src="images/intro/response.jpg" class="center" alt="User interacting with a website.">

To write performant sites and apps you need to understand how HTML, JavaScript and CSS is handled by the browser, and ensure that the code you write (and the other 3rd party code you include) runs as efficiently as possible.

## 60fps and Device Refresh Rates

Most devices today refresh their screens **60 times a second**. If there’s an animation or transition running, or the user is scrolling the pages, the browser needs to match the device’s refresh rate and put up 1 new picture, or frame, for each of those screen refreshes.

Each of those frames has a budget of just over 16ms (1 second / 60 = 16.66ms). In reality, however, the browser has housekeeping work to do, so all of your work needs to be completed inside **10ms**. When you fail to meet this budget the frame rate drops, and the content judders on screen. This is often referred to as **jank**, and it negatively impacts the user's experience.

## The pixel pipeline
There are five major areas that you need to know about and be mindful of when you work. They are areas you have the most control over, and key points in the pixels-to-screen pipeline:

<img src="images/intro/frame-full.jpg" class="center" alt="The full pixel pipeline">

* **JavaScript**. Typically JavaScript is used to handle work that will result in visual changes, whether it’s jQuery’s `animate` function, sorting a data set, or adding DOM elements to the page. It doesn’t have to be JavaScript that triggers a visual change, though: CSS Animations, Transitions, and the Web Animations API are also commonly used.
* **Style calculations**. This is the process of figuring out which CSS rules apply to which elements based on matching selectors, e.g. `.headline` or `.nav > .nav__item`.
* **Layout**. Once the browser knows which rules apply to an element it can begin to calculate how much space it takes up and where it is on screen. The web’s layout model means that one element can affect others, e.g. the width of the `<body>` element typically affects its children’s widths and so on all the way up and down the tree, so the process can be quite involved for the browser.
* **Paint**. Painting is the process of filling in pixels. It involves drawing out text, colors, images, borders, and shadows, essentially every visual part of the elements. The drawing is typically done onto multiple surfaces, often called layers.
* **Compositing**. Since the parts of the page were drawn into potentially multiple layers they need to be drawn to the screen in the correct order so that the page renders correctly. This is especially important for elements that overlap another, since a mistake could result in one element appearing over the top of another incorrectly.

{% include modules/remember.liquid title="Remember" list=page.notes.pipelinecosts %}

You won’t always necessarily touch every part of the pipeline on every frame. In fact, there are three ways the pipeline _normally_ plays out for a given frame when you make a visual change, either with JavaScript, CSS, or Web Animations:

### 1. JS / CSS > Style > Layout > Paint > Composite

<img src="images/intro/frame-full.jpg" class="center" alt="The full pixel pipeline">

If you change a “layout” property, so that’s one that changes an element’s geometry, like its width, height, or its position with left or top, the browser will have to check all the other elements and “reflow” the page. Any affected areas will need to be repainted, and the final painted elements will need to be composited back together.

### 2. JS / CSS > Style > Paint > Composite

<img src="images/intro/frame-no-layout.jpg" class="center" alt="The  pixel pipeline without layout.">

If you changed a “paint only” property, like a background image, text color, or shadows, i.e. one that does not affect the layout of the page, then the browser skips layout, but it will still do paint.

### 3. JS / CSS > Style > Composite

<img src="images/intro/frame-no-layout-paint.jpg" class="center" alt="The pixel pipeline without layout or paint.">

If you change a property that requires neither layout nor paint, and the browser jumps to just do compositing.

This final version is the cheapest and most desirable for high pressure points in an app's lifecycle, like animations or scrolling.

{% include modules/remember.liquid title="Note" list=page.notes.csstriggers %}

Let’s take a dive into the different parts of the pipeline. We’ll take a look at the common issues, as well how to diagnose and fix them.

{% endwrap%}
