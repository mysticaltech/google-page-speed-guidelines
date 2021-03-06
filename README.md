Google Page Speed Insight Rules:
===========

## 0. [Install Google Page Speed Module](https://developers.google.com/speed/pagespeed/module/)
> Google’s pagespeed module gives you a number of tools that can speed up front-end performance by making minor tweaks (trimming the domain out of local URLs, inserting DNS prefetch tags, collapsing whitespace) or major ones (lazy-loading images)

## 1. Avoid Landing Page Redirects
> Avoid having more than one redirects from the given url to the final landing page.

## 2. Enable Compression
> Enable that the assets served to clients are in gzip compression.

* Use [Rack::Deflater](https://robots.thoughtbot.com/content-compression-with-rack-deflater)
* Use [Nginx Config](https://richonrails.com/articles/enabling-compression-for-nginx)
* [Gzipping font files](http://www.phpied.com/gzip-your-font-face-files/)

## 3. Improving Server Response Time
> Ensure that the server response time is not more than 200 ms.

* Analyse application logic and response from server, and fix the source that gives a slow server response time.
* Use [Rack Mini Profiler](https://github.com/MiniProfiler/rack-mini-profiler) to check the page speed and server response time.
* Avoid bad requests.
* Use CDN to host images & other static assets.
* Use [SPDY on Rails](https://bugsnag.com/blog/spdy-on-rails)
* Use Rails Caching - Refer to [Rails Official Guide](http://guides.rubyonrails.org/caching_with_rails.html) and [Blog - Complete guide on Rails Caching](http://nateberkopec.com/2015/07/15/the-complete-guide-to-rails-caching.html).

## 4. Leverage browser caching
> Ensure that the response from server includes caching headers or the resources are specified to be cached for only a short time.

* Move favicon.ico to assets folder and use `favicon_link_tag`.
* Use the following Nginx config to ensure http cache-control on images in /public/images directory:
```
# Media: images, icons, video, audio, HTC
location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc)$ {
  expires 1M;
  access_log off;
  add_header Cache-Control "public";
}
```
* If enable Gzip compression and want to use Rails Http Caching, upgrade to `nginx 1.7.3 or higher`, and use [Rails weak etags](https://github.com/johnnaegle/rails_weak_etags) gem. The reason is that Nginx strip etags by default. More details: [Rails & Weak ETags](http://stackoverflow.com/questions/18693718/weak-etags-in-rails)
* ***Check above point if want to use Rails HTTP Caching***. Configure Rails to validate cached responses with ETags (Conditional HTTP Caching). 
  * [Introduction to Conditional HTTP Caching wit h Rails](https://robots.thoughtbot.com/introduction-to-conditional-http-caching-with-rails)
  * [How etags works in Rails](http://mohanraj-nagasamy.github.io/blog/2014/02/22/browser-cache-how-etags-works-in-rails-3-and-rails-4/)
  * [RailsCast - HTTP Caching](https://www.youtube.com/watch?v=8iCPR9BqlNA)

## 5. Minify Assets (CSS, and JavaScript)
> Ensure that the CSS and JavaScript assets could be reduced through minification.

* minify your CSS, and JavaScript resources. This is done by default through sprockets and uglifier in Rails.

## 6. Optimize Images
> Reduce the filesize of ithe images without significantly impacting their visual quality.

* Use [Phil's Easy Optimise](https://github.com/PhilipCastiglione/easy_optimise) or [Kraken.io](https://kraken.io/) to optimise images.
* Use progressive JPEG.

## 7. Optimize CSS Delivery
> Avoid including render blocking external stylesheets.

* No @import calls for CSS.
* No more than one external CSS stylesheet of an reasonable size (less than 75k or so).
* Inline small CSS into HTML using style tags for above the fold content.
* No CSS in HTML things like your divs or your h1s (in element CSS).

## 8. Reduce the size of the above-the-fold content
> Reduce network round trips that are required to render the above the fold content of the page.

* Enable Keep-Alive HTTP connection.
* Structure your HTML to load the critical, above-the-fold content first.


## 9. Remove Render-Blocking JavaScript
> Avoid referencing a blocking external JavaScript file in the above-the-fold portion of the page.

* Use [the new Typekit embeded code](http://blog.typekit.com/2015/08/04/new-embed-code-for-asynchronous-font-loading/) to load typekit asynchronously
* If using [Sharethis JS plugin](http://www.sharethis.com/), Use [Asynchronous Sharethis Loading](https://techblog.willshouse.com/2014/06/07/sharethis-asynchronous-javascript-loading/) instead.
* Enable asynchronous loading of scripts in Production environment:

`<%= javascript_include_tag 'application', async: Rails.env.production? %>`

## 10. Use Asynchronous Scripts
> Leverage asynchronous loading of scripts.


