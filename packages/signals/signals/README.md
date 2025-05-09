#  @segment/analytics-signals 


## Settings / Configuration

See: [settings.ts](src/types/settings.ts)

## Quick start

## Snippet Users
```html
<head>
  <title>My Website</title>
  
  <!-- Load Segment (find and replace 'MY_WRITEKEY')  -->
  <script>
    !function(){var i="analytics",analytics=window[i]=window[i]||[];if(!analytics.initialize)if(analytics.invoked)window.console&&console.error&&console.error("Segment snippet included twice.");else{analytics.invoked=!0;analytics.methods=["trackSubmit","trackClick","trackLink","trackForm","pageview","identify","reset","group","track","ready","alias","debug","page","screen","once","off","on","addSourceMiddleware","addIntegrationMiddleware","setAnonymousId","addDestinationMiddleware","register"];analytics.factory=function(e){return function(){if(window[i].initialized)return window[i][e].apply(window[i],arguments);var n=Array.prototype.slice.call(arguments);if(["track","screen","alias","group","page","identify"].indexOf(e)>-1){var c=document.querySelector("link[rel='canonical']");n.push({__t:"bpc",c:c&&c.getAttribute("href")||void 0,p:location.pathname,u:location.href,s:location.search,t:document.title,r:document.referrer})}n.unshift(e);analytics.push(n);return analytics}};for(var n=0;n<analytics.methods.length;n++){var key=analytics.methods[n];analytics[key]=analytics.factory(key)}analytics.load=function(key,n){var t=document.createElement("script");t.type="text/javascript";t.async=!0;t.setAttribute("data-global-segment-analytics-key",i);t.src="https://cdn.segment.com/analytics.js/v1/" + key + "/analytics.min.js";var r=document.getElementsByTagName("script")[0];r.parentNode.insertBefore(t,r);analytics._loadOptions=n};analytics._writeKey="MY_WRITEKEY";;analytics.SNIPPET_VERSION="5.2.0";
    analytics.page();
    }}();
  </script>
  <!-- Register SignalsPlugin -->
  <script src="https://cdn.jsdelivr.net/npm/@segment/analytics-signals@latest/dist/umd/analytics-signals.umd.js"></script>
  <script>
    var signalsPlugin = new SignalsPlugin()
    analytics.register(signalsPlugin)
    analytics.load(analytics._writeKey)
  </script>

</head>
```

## `npm` Users
### Installation
```bash
# npm
npm install @segment/analytics-signals
# yarn
yarn add @segment/analytics-signals
# pnpm
pnpm install @segment/analytics-signals
```

```ts
// analytics.js/ts
import { AnalyticsBrowser } from '@segment/analytics-next'
import { SignalsPlugin } from '@segment/analytics-signals'

export const analytics = new AnalyticsBrowser()
export const signalsPlugin = new SignalsPlugin()

analytics.register(signalsPlugin)

analytics.load({
  writeKey: '<YOUR_WRITE_KEY>'
})

```
### Sending and Viewing Signals on Segment.com (Debug mode)
For privacy reasons, **signals are only to Segment.com when debug mode is on**. You must enable debug mode on the client to send and view those signals on segment.com. To enable debug mode on your client
```
https://my-website.com?segment_signals_debug=true
```
You can *turn off debugging* by doing:
```
https://my-website.com?segment_signals_debug=false
```

* This also logs all signals to the js console.

#### Alternative method(s) of enabling debug mode
### Enable debug mode from your JS console:
```js
window.SegmentSignalsPlugin.debug() 
```

### Extending / Emitting Custom Signals
```ts
import { signalsPlugin } from './analytics' // assuming you exported your plugin instance.

signalsPlugin.addSignal({ someData: 'foo' }) 

// emits a signal with the following shape
{ 
  type: 'userDefined' 
  data: { someData: 'foo', ...  }
}
```

### Sandbox Strategies
If getting CSP errors, you can use the experimental 'global' sandbox strategy.

```ts
new SignalsPlugin({ sandboxStrategy: 'global' })
```


### Debugging  
Debug mode **MUST** be enabled on the client to VIEW signals on segment.com.



### Advanced

#### Listening to signals
```ts
const signalsPlugin = new SignalsPlugin()

signalsPlugin.onSignal((signal) => console.log(signal))
```


### Middleware / Plugins
#### Drop or modify signals
```ts
import { SignalsPlugin, SignalsMiddleware } from '@segment/analytics-signals'

class MyMiddleware implements SignalsMiddleware {
  process(signal: Signal) {
    // drop all instrumentation signals
    if (
       signal.type === 'instrumentation'
    ) {
      return null;
    } else {
      return signal;
    }
  }
}

const signalsPlugin = new SignalsPlugin({ 
  middleware: [
    new MyMiddleware()
  ]
})
analytics.register(signalsPlugin)
```


### Playground / Development / Testing
See the [signals example repo](../signals-example).

## Signal Types

### `interaction`
Interaction signals emit in response to a user interaction

### `instrumentation`
Instrumentation signals emit whenever a Segment event is emitted.

### `navigation`
Instrumentation signals emit whenever the URL changes.

> Note: you can also rely on the initial analytics.page() call, which you can access as an Instrumentation signal.

### `network`
Network signals emit when an HTTP Request is made, or an HTTP Response is received. To emit a network signal, the network activity must have the following requirements:
- Initiated using the `fetch` API
- First party domain (e.g if on `foo.com`, then `foo.com/api/products`, but not `bar.com/api/products`)
- Contains the content-type: `application/json`
