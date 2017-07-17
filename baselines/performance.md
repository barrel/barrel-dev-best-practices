### Barrel Development Best Practices

# Performance
 
### Minification/Concat JS/CSS
- Establish workflow for minimizing asset groups at the beginning of the project. (It's probably Grunt. Gulp?)
- If you're using an existing framework utilize it's asset pipeline. (Rails, Shopify)
- Always create source maps for minified assets.
- If using assistant tasks, like Autoprefixer, declare minimum browser version support.

### Handle Requests Responsibly
Minify scripts, but not to the point of squeezing requests into too large of chunks.

- Determine what CSS and JS is necessary on page load.
- Minimize the use of blocking CSS and JS libraries.
- Stagger and defer problematic or non-essential scripts.
- Thread large JS scripts by not minifying them with others.

For problematic libraries like Facebook and YouTube, use asynchronous loading and callbacks to determine when the scripts are loaded and then execute.

### Server-side Caching*
Caching is a really great thing. USE IT! Often platforms have caching built right in, and the process of activating it is simply flipping a switch. If not, plugins exist for nearly every platform to make it possible.

Server-side caching cannot always be a "full-page" cache since many sites have dynamic elements that change and need to change frequently (ecommerce, web-apps). This doesn't mean you can't use caching. Some options still exist:

#### Partial Caching
Partial or "fragment" caching uses key/value datastores like Redis or Memcache to store smaller parts of the page in the cache to quickly process smaller sections that have a longer refresh rate. These can usually be activated through framework extensions (see Rails, Magento, Wordpress, Shopify).

#### DB Caching
Database caching remembers common queries and their results so that they don't need to be called over and over. This is also referred to as Model Caching.

#### Smart Back-end Code
- Minimize repetitious queries by storing common data in class variables that can be checked prior to a new database request.
- Consider placing data in unique columns over joining tables when dealing with large amounts of data.
- When connecting to an API periodically cache to a database or json files on a cron. Reference the locally cached data at runtime to avoid external latency.
- Check logs for errors. A bad request, missing resource, or malformed code could be eating up server CPU without showing errors on the front-end.

#### Common Settings / Considerations
- Cache / datastore freshness and age
- Refresh triggers (database, foreign key)

### Client-side Caching
- Use "Web Page Test" ya dummy!

#### Version string
Append version string for cached assets so you know you'll be getting the latest.

- Make sure the versions string is based on when the asset was saved, not the time is was looked up.
- Defer browser caching until launch, as not to mess with QA and provide realistic first-view times.
- Set `max-age = "1" # Like, never` during development.*
- Set `max-age = "2592000" # 30 days` pre-launch.*
- See [this article](http://www.mobify.com/blog/beginners-guide-to-http-cache-headers/)

```
<script type="text/javascript" src="http://www.foobar.com/public/scripts/main.js?v=20150204121515" />
```

Version strings may be ineffective for particular CDNs. For example CloudFront provides the option of ignoring version strings when setting up the distribution. Be mindful of how your CDN will process the asset.

#### CDNs
A CDN (content delivery network) is a distributed network that will provide web assets to the client from the closest available location to limit network latency. You should totally set one of these up on all of your projects.

- When scoping projects, CDN should be part of the recommendation and budgeting.
- CDN should be set up early in a project's lifecycle and used for both staging and production environments.

For more information on setting up a CDN, please refer to [Dev How-Tos: CDN](https://github.com/barrel/barrel-dev-how-tos/blob/master/cdns.md).

#### Domain Sharding
Domain sharding is the process of separating assets to different domains so that more things can be downloaded simultaneously (6 threads per domain).

- Separate and serve sets of assets from different domains on your CDN: CSS/JavaScript, static images, user uploads, etc.
- More domains != more speed, typically content from the network should not be served from more than three domains (local, 2 x CDN)

#### Managing Slow Libraries
Some examples of really slow libraries: Facebook, Twitter, LinkedIn, social integrations in general, RT Pixel, Mixpanel, Hubspot, user lead tracking in general.

- Offload scripts for slow libraries so that they load either at the end of the page or asynchronously (if that option is available).
- Manage project expectations surrounding social integrations and other slow libraries.
- Use back-end integrations when possible (Intercom, Mixpanel, Hubspot, etc).
- When possible, don't block page render with lead tracking.

#### Gzip Compression
Enabling compression of text based assets using Gzip will typically reduce file sizes by ~50%. This means less time spent by the client downloading assets, and less bandwidth used overall.

- This form of compression is typically enabled by default but can be checked using a tool such as WebPagetest
- Gzipping can be enabled via htaccess if it isn't already

#### Keep-Alive
Each new TCP connection requires a three-way handshake between the server and client to negotiate and start a TCP session. This handshake amounts to a full round trip of network latency before data is transmitted. A server setting known as "keep-alive" keeps connections between the server and client open for a configurable period to avoid the requirement for additional handshakes.

- To determine if keep-alive is on, use WebPagetest
- If keep-alive isn't enabled the server configuration should be modified (most likely this will be done through one of Apache's configuration files)
- The ideal values for keep-alive's various parameters differ from environment to environment, and should be tuned accordingly
- Keep-alive should be tuned before an environment enters production

#### SSL
The number of round trips required to establish an SSL/TLS connection (3 round trips vs. 1 for a standard TCP connection, assuming ideal conditions for both) result in a significant amount of additional network latency for new connections. Due to this additional overhead SSL/TLS should only be used when necessary.

- If a client has requested that an entire site be accessed via HTTPS, even where the information being transmitted doesn't require encryption, inform them of the performance consequences.
- Ensure that keep-alive has been enabled on any environments transmitting data via SSL/TLS to limit the number of new connections being made.
