## A `scrollend` Explainer

Update April 3, 2023:  
This is now on [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/scrollend_event) and shipping in browsers:
- Firefox 109
- Chrome 114

### Problem Statement
Web developers often watch scroll events to synchronize ancillary elements, fetch data, trigger animations, and more. Today watching scroll events is done with the [`scroll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/scroll_event) event. But if developers want to know when scroll has ended, or rested and isn't moving anymore, there currently is no event. To work around this, developers set a timeout and/or check scroll positions, to [attempt an estimated scrollend event](https://gomakethings.com/detecting-when-a-visitor-has-stopped-scrolling-with-vanilla-javascript/), like so:

```js
element.addEventListener('scroll', () => {
  clearTimeout(element.scrollEndTimer);              
  element.scrollEndTimer = setTimeout(synchronizeFn, 100);
});
```

The fastest this function can determine when scroll has ended is `100ms`. It's also quite wasteful as it overrides the previous timeout on every scroll tick. Browser engines don't like this waste and developers don't like this delay. Furthermore, this strategy will fire `synchronizeFn()` if a user has scrolled with their finger and paused for 100ms, essentially prematurely firing the event because scroll hasn't changed, but the user isn't done.

### Use Cases

#### #1 Scrollable Tabs

https://user-images.githubusercontent.com/1134620/206300052-77136c7c-84a4-4ca0-9d13-0b41172c4795.mp4

https://codepen.io/argyleink/pen/JjXoXVe

#### #2 Carousel with dots and animation

https://user-images.githubusercontent.com/1134620/206300625-7164f650-48e6-43bd-873f-42608cf473ba.mp4

https://codepen.io/argyleink/pen/jOzEpjG/e26504e41694bd6267469ba23effae30

#### #3 Determining which element has snapped

https://user-images.githubusercontent.com/1134620/206301175-e18fca95-e05e-45ee-aaf4-b15a6f594fab.mp4

https://codepen.io/argyleink/pen/wveVPom/17a5b2ee3236403d369881d816b0f03d

### Solution
A [`scrollend`](https://drafts.csswg.org/cssom-view/#scrolling-events) event that takes into account user interaction, proper timing, and the visual viewport.

```js
element.addEventListener('scrollend', () => {
  synchronizeFn()
});
```
