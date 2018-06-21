# AdSense

Configure the [asynchronous ad code](https://support.google.com/adsense/answer/3221666) without the `<script>` tag.

``` html
<ins class="adsbygoogle" style="display: inline-block; width: 728px; height: 90px" data-ad-client="ca-pub-xxxxxxxxxxxxxxxx" data-ad-slot="1234567890"></ins>
```

**Tip:** avoid use `style` attribute creating classes on you stylesheet.

Create the `adsense.js` component.

``` js
var AdSense = function() {
  var slots = [];

  function parseSlots(className) {
    className = className || 'adsbygoogle';
    slots = document.getElementsByClassName(className);
    return slots;
  }

  function refresh(slot) {
    if (!slot) {
      throw new Error('Please send a valid slot element.');
    }

    (adsbygoogle = window.adsbygoogle || []).push({});
  }

  function reset() {
    delete window.adsbygoogle;
  }

  function updateLocation() {
    parseSlots();

    for (var index = 0; index < slots.length; index++) {
      refresh(slots[index]);
    }
  }

  parseSlots();

  return {
    parseSlots: parseSlots,
    refresh: refresh,
    reset: reset,
    slots: slots,
    updateLocation: updateLocation
  };
}
```

Initialize the library and bind the events.

``` js
(function(document, window) {
  var firstPageview = true;

  function ready() {
    window.adsense = new AdSense();
  }
  
  function update() {
    if (!firstPageview) {
      window.adsense.updateLocation();
    }

    firstPageView = false;
  }

  function leave() {
    window.adsense.reset();
  }
  
  document.addEventListener('DOMContentLoaded', ready);
  document.addEventListener('turbolinks:load', update);
  document.addEventListener('turbolinks:before-visit', leave);
}(document, window));
```

### Improvements

- [ ] Study how `window.google_` variables affect ads.

``` js
Object.keys(window).filter(key => key.match(/goog/gi))
```
