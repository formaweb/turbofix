# Facebook

Insert the application identifier in `meta` tag.

``` html
<meta property="fb:app_id" content="0000000000">
```

Load the Facebook `script` in the `<head>`. You can replace the `en_US` by your country language, like `pt_BR`.

``` html
<script src="https://connect.facebook.net/en_US/sdk.js" async></script>
```

Create the `facebook.js` component.

``` js
var Facebook = function(identifier, version) {
  var _queue = [],
      _isLoaded = false;

  function push(callback) {
    if (_isLoaded) {
      return callback();
    }

    return _queue.push(callback);
  }

  function setup() {
    _isLoaded = true;

    FB.init({
      appId: identifier,
      cookie: true,
      status: true,
      version: version,
      xfbml: true
    });

    _queue.forEach(function(callback) {
      callback();
    })
  }

  function updateLocation() {
    push(function() {
      FB.AppEvents.logPageView();
      FB.XFBML.parse();
    });
  }

  return {
    push: push,
    setup: setup,
    updateLocation: updateLocation
  };
}
```

By default, we instantiate our library, which will initialize Facebook and will send pageview event and parse plugins on page change. 

``` js
(function(document) {
  function ready(event) {
    var identifier = document.querySelector('[property="fb:app_id"]').content;

    window.facebook = new Facebook(identifier, 'v2.12');
    window.facebook.setup();
  }
  
  function update(event) {
    if (event.data.timing.visitStart) {
      window.facebook.updateLocation();
    }
  }
});

document.addEventListener('DOMContentLoaded', ready);
document.addEventListener('turbolinks:load', update);
```
